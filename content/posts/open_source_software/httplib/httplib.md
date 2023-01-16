---
title: "开源软件:httplib"
date: 2023-01-16T17:52:52+08:00
draft: false
description: "开源软件"
lightgallery: true
tags: [httplib]
categories: [开源软件]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。


### explicit & implicit 
 C++中的explicit关键字只能用于修饰只有一个参数的类构造函数, 它的作用是表明该构造函数是显示的, 而非隐式的, 跟它相对应的另一个关键字是implicit, 意思是隐藏的,类构造函数默认情况下即声明为implicit(隐式).

### std::streambuf
https://blog.csdn.net/man_sion/article/details/78110842


using ContentProvider = std::function<bool(size_t offset, size_t length, DataSink &sink)>;
using ContentProviderWithoutLength = std::function<bool(size_t offset, DataSink &sink)>;
using ContentProviderResourceReleaser = std::function<void(bool success)>;
using ContentReceiverWithProgress = std::function<bool(const char *data, size_t data_length, uint64_t offset,uint64_t total_length)>;
using ContentReceiver = std::function<bool(const char *data, size_t data_length)>;
using MultipartContentHeader = std::function<bool(const MultipartFormData &file)>;

class ContentReader {
public:
  using Reader = std::function<bool(ContentReceiver receiver)>;
  using MultipartReader = std::function<bool(MultipartContentHeader header,
                                             ContentReceiver receiver)>;

  ContentReader(Reader reader, MultipartReader multipart_reader)
      : reader_(std::move(reader)),
        multipart_reader_(std::move(multipart_reader)) {}

  bool operator()(MultipartContentHeader header,
                  ContentReceiver receiver) const {
    return multipart_reader_(std::move(header), std::move(receiver));
  }

  bool operator()(ContentReceiver receiver) const {
    return reader_(std::move(receiver));
  }

  Reader reader_;
  MultipartReader multipart_reader_;
};


class Stream {
public:
  virtual ~Stream() = default;

  virtual bool is_readable() const = 0;
  virtual bool is_writable() const = 0;

  virtual ssize_t read(char *ptr, size_t size) = 0;
  virtual ssize_t write(const char *ptr, size_t size) = 0;
  virtual void get_remote_ip_and_port(std::string &ip, int &port) const = 0;
  virtual socket_t socket() const = 0;

  template <typename... Args>
  ssize_t write_format(const char *fmt, const Args &...args);
  ssize_t write(const char *ptr);
  ssize_t write(const std::string &s);
};


class TaskQueue {
public:
  TaskQueue() = default;
  virtual ~TaskQueue() = default;

  virtual void enqueue(std::function<void()> fn) = 0;
  virtual void shutdown() = 0;

  virtual void on_idle() {}
};


class ThreadPool : public TaskQueue {
public:
  explicit ThreadPool(size_t n) : shutdown_(false) {
    while (n) {
      threads_.emplace_back(worker(*this));
      n--;
    }
  }

  ThreadPool(const ThreadPool &) = delete;
  ~ThreadPool() override = default;

  void enqueue(std::function<void()> fn) override {
    std::unique_lock<std::mutex> lock(mutex_);
    jobs_.push_back(std::move(fn));
    cond_.notify_one();
  }

  void shutdown() override {
    // Stop all worker threads...
    {
      std::unique_lock<std::mutex> lock(mutex_);
      shutdown_ = true;
    }

    cond_.notify_all();
    
    // Join...
    for (auto &t : threads_) {
      t.join();
    }
  }

private:
  struct worker {
    explicit worker(ThreadPool &pool) : pool_(pool) {}

    void operator()() {
      for (;;) {
        std::function<void()> fn;
        {
          std::unique_lock<std::mutex> lock(pool_.mutex_);
    
          pool_.cond_.wait(
              lock, [&] { return !pool_.jobs_.empty() || pool_.shutdown_; });
    
          if (pool_.shutdown_ && pool_.jobs_.empty()) { break; }
    
          fn = pool_.jobs_.front();
          pool_.jobs_.pop_front();
        }
    
        assert(true == static_cast<bool>(fn));
        fn();
      }
    }
    
    ThreadPool &pool_;
  };
  friend struct worker;

  std::vector<std::thread> threads_;
  std::list<std::function<void()>> jobs_;

  bool shutdown_;

  std::condition_variable cond_;
  std::mutex mutex_;
};

std::vector<std::thread> threads_;
ThreadPool &pool_;
std::list<std::function<void()>> jobs_;

using SocketOptions = std::function<void(socket_t sock)>;



line_reader









```C++
inline bool is_file(const std::string &path) {
  struct stat st;
  return stat(path.c_str(), &st) >= 0 && S_ISREG(st.st_mode);
}

inline bool is_dir(const std::string &path) {
  struct stat st;
  return stat(path.c_str(), &st) >= 0 && S_ISDIR(st.st_mode);
}

inline bool is_valid_path(const std::string &path) {
  size_t level = 0;
  size_t i = 0;

  // Skip slash
  while (i < path.size() && path[i] == '/') {
    i++;
  }

  while (i < path.size()) {
    // Read component
    auto beg = i;
    while (i < path.size() && path[i] != '/') {
      i++;
    }

    auto len = i - beg;
    assert(len > 0);

    if (!path.compare(beg, len, ".")) {
      ;
    } else if (!path.compare(beg, len, "..")) {
      if (level == 0) { return false; }
      level--;
    } else {
      level++;
    }

    // Skip slash
    while (i < path.size() && path[i] == '/') {
      i++;
    }
  }

  return true;
}

inline void read_file(const std::string &path, std::string &out) {
  std::ifstream fs(path, std::ios_base::binary);
  fs.seekg(0, std::ios_base::end);
  auto size = fs.tellg();
  fs.seekg(0);
  out.resize(static_cast<size_t>(size));
  fs.read(&out[0], static_cast<std::streamsize>(size));
}

inline std::string file_extension(const std::string &path) {
  std::smatch m;
  static auto re = std::regex("\\.([a-zA-Z0-9]+)$");
  if (std::regex_search(path, m, re)) { return m[1].str(); }
  return std::string();
}

inline bool is_space_or_tab(char c) { return c == ' ' || c == '\t'; }

inline std::pair<size_t, size_t> trim(const char *b, const char *e, size_t left,
                                      size_t right) {
  while (b + left < e && is_space_or_tab(b[left])) {
    left++;
  }
  while (right > 0 && is_space_or_tab(b[right - 1])) {
    right--;
  }
  return std::make_pair(left, right);
}

inline std::string trim_copy(const std::string &s) {
  auto r = trim(s.data(), s.data() + s.size(), 0, s.size());
  return s.substr(r.first, r.second - r.first);
}

inline void split(const char *b, const char *e, char d,
                  std::function<void(const char *, const char *)> fn) {
  size_t i = 0;
  size_t beg = 0;

  while (e ? (b + i < e) : (b[i] != '\0')) {
    if (b[i] == d) {
      auto r = trim(b, e, beg, i);
      if (r.first < r.second) { fn(&b[r.first], &b[r.second]); }
      beg = i + 1;
    }
    i++;
  }

  if (i) {
    auto r = trim(b, e, beg, i);
    if (r.first < r.second) { fn(&b[r.first], &b[r.second]); }
  }
}
```

trim : 修剪





```c++
class Stream {
public:
  virtual ~Stream() = default;

  virtual bool is_readable() const = 0;
  virtual bool is_writable() const = 0;

  virtual ssize_t read(char *ptr, size_t size) = 0;
  virtual ssize_t write(const char *ptr, size_t size) = 0;
  virtual void get_remote_ip_and_port(std::string &ip, int &port) const = 0;
  virtual socket_t socket() const = 0;

  template <typename... Args>
  ssize_t write_format(const char *fmt, const Args &...args);
  ssize_t write(const char *ptr);
  ssize_t write(const std::string &s);
};
```



```
template <typename T>
bool read_content(Stream &strm, T &x, size_t payload_max_length, int &status,
                  Progress progress, ContentReceiverWithProgress receiver,
                  bool decompress) {
  return prepare_content_receiver(
      x, status, std::move(receiver), decompress,
      [&](const ContentReceiverWithProgress &out) {
        auto ret = true;
        auto exceed_payload_max_length = false;

        if (is_chunked_transfer_encoding(x.headers)) {
          ret = read_content_chunked(strm, out);
        } else if (!has_header(x.headers, "Content-Length")) {
          ret = read_content_without_length(strm, out);
        } else {
          auto len = get_header_value<uint64_t>(x.headers, "Content-Length");
          if (len > payload_max_length) {
            exceed_payload_max_length = true;
            skip_content_with_length(strm, len);
            ret = false;
          } else if (len > 0) {
            ret = read_content_with_length(strm, len, std::move(progress), out);
          }
        }

        if (!ret) { status = exceed_payload_max_length ? 413 : 400; }
        return ret;
      });
}
```





https://leon-liu.blog.csdn.net/article/details/53432408?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ELandingCtr%7Edefault-1.queryctr&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ELandingCtr%7Edefault-1.queryctr&utm_relevant_index=2



http://www.httpwatch.com/httpgallery/chunked/chunkedimage.aspx



client 

  std::array<char, 2048> buf{}; 

httplib socket recv 有默认大小设置

如果传输是支持分片的 （chunked）则接收可以分片传输



swerver 静态文件服务 默认是range 下发



httplib 由 socket  接收数据 ：

1. 先申请 2048 的数据  std::array<char, 2048> buf{};   ，用于接收头数据
2. 解析头数据 
   - 如果是 chunked （"Transfer-Encoding", "chunked"）分片下载则可以通过注册的回调函数 分片拿到数据
   - 如果 Content-Length 有数据则按照 且不是 chunked 模式则按照 Content-Length 读取数据(如果 Content-Length 超过设置设置大小 则 报错 ，返回HTTP 状态码 ： 413  "Payload Too Large“)
   - 如果没有 Content-Length  则按照默认recv（CPPHTTPLIB_RECV_BUFSIZ (4096） 大小下载 



结论 ： 目前httplib 如果想实现边下载边落盘的功能，只能是  协议上支持 range 或者 chunked 模式。

-  range 方式实现是 应用层多次请求不同range 然后写入文件

- chunked 方式是 可以注册回调函数，一次请求多次回调内实现多次由 socket 内读取数据

如果服务器并没有支持 chunked / range 方式 ，基于 httplib 库无法实现 边读socket 边落盘方式 （这种我理解不是标准的 http 交互的行为）





1. 如果 chunked 

CPPHTTPLIB_RECV_BUFSIZ







```C++
template <typename T>
bool read_content(Stream &strm, T &x, size_t payload_max_length, int &status,
                  Progress progress, ContentReceiverWithProgress receiver,
                  bool decompress) {
  return prepare_content_receiver(
      x, status, std::move(receiver), decompress,
      [&](const ContentReceiverWithProgress &out) {
        auto ret = true;
        auto exceed_payload_max_length = false;

        if (is_chunked_transfer_encoding(x.headers)) {
          ret = read_content_chunked(strm, out);
        } else if (!has_header(x.headers, "Content-Length")) {
          ret = read_content_without_length(strm, out);
        } else {
          auto len = get_header_value<uint64_t>(x.headers, "Content-Length");
          if (len > payload_max_length) {
            exceed_payload_max_length = true;
            skip_content_with_length(strm, len);
            ret = false;
          } else if (len > 0) {
            ret = read_content_with_length(strm, len, std::move(progress), out);
          }
        }

        if (!ret) { status = exceed_payload_max_length ? 413 : 400; }
        return ret;
      });
}
```



```C++
inline bool read_content_with_length(Stream &strm, uint64_t len,
                                     Progress progress,
                                     ContentReceiverWithProgress out) {
  char buf[CPPHTTPLIB_RECV_BUFSIZ];

  uint64_t r = 0;
  while (r < len) {
    auto read_len = static_cast<size_t>(len - r);
    auto n = strm.read(buf, (std::min)(read_len, CPPHTTPLIB_RECV_BUFSIZ));
    if (n <= 0) { return false; }

    if (!out(buf, static_cast<size_t>(n), r, len)) { return false; }
    r += static_cast<uint64_t>(n);

    if (progress) {
      if (!progress(r, len)) { return false; }
    }
  }

  return true;
}
```





[DrHttp] 更新DrHttp 实现
+) 增加form-data 数据上传
