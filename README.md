# 🌐 Webserv

A lightweight, high-performance HTTP/1.1 web server written in C++98. This project implements a fully functional web server capable of handling multiple simultaneous connections, serving static files, processing CGI scripts, and managing file uploads.

---

## 📋 Table of Contents

- [Features](#-features)
- [Requirements](#-requirements)
- [Installation](#-installation)
- [Usage](#-usage)
- [Configuration](#-configuration)
- [Project Structure](#-project-structure)
- [HTTP Methods](#-http-methods)
- [CGI Support](#-cgi-support)
- [Session Management](#-session-management)
- [Testing](#-testing)
- [Authors](#-authors)

---

## ✨ Features

- **HTTP/1.1 Compliant**: Full support for HTTP/1.1 protocol
- **Non-blocking I/O**: Uses `poll()` for efficient event-driven architecture (Reactor pattern)
- **Multiple Server Blocks**: Configure multiple virtual servers on different ports
- **Static File Serving**: Serve HTML, CSS, JS, images, and other static content
- **CGI Support**: Execute scripts in Python, PHP, Bash, Perl, and more
- **File Uploads**: Handle multipart/form-data file uploads
- **Session Management**: Cookie-based session handling
- **Directory Listing**: Optional auto-index for directory browsing
- **Custom Error Pages**: Configurable error pages (404, 405, 500, etc.)
- **URL Redirects**: Support for HTTP redirects
- **Request Body Limits**: Configurable maximum client body size
- **Graceful Shutdown**: Handle SIGINT and SIGTERM signals properly

---

## 📦 Requirements

- **Compiler**: C++ compiler with C++98 support (g++, clang++)
- **OS**: Linux / macOS
- **Make**: GNU Make
- **Optional** (for CGI):
  - Python 3
  - PHP-CGI
  - Bash

---

## 🔧 Installation

### Clone the Repository

```bash
git clone https://github.com/Youness-Tr/Webserver.git
cd Webserver
```

### Build the Project

```bash
make
```

This will compile the project and create the `Webserv` executable.

### Other Make Commands

| Command | Description |
|---------|-------------|
| `make` | Build the project |
| `make clean` | Remove object files |
| `make fclean` | Remove object files and executable |
| `make re` | Rebuild the project from scratch |

---

## 🚀 Usage

### Start the Server

```bash
# Using default configuration (config/default.conf)
./Webserv

# Using a custom configuration file
./Webserv config/config.conf
```

### Access the Server

Open your browser and navigate to:
```
http://localhost:8080
```

The port depends on your configuration file settings.

### Stop the Server

Press `Ctrl+C` to gracefully shut down the server.

---

## ⚙️ Configuration

The server is configured using a custom configuration file. Below is the syntax and available options:

### Basic Server Block

```nginx
server
{
    host = 0.0.0.0;              # IP address to bind to
    port = 8080;                  # Port to listen on (can specify multiple)
    server_name = example.com;    # Server name for virtual hosting

    client_max_body_size = 5M;    # Maximum request body size (K, M, G)

    # Custom error pages
    error_page 404 www/errp/404.html;
    error_page 500 www/errp/500.html;

    # Route configuration
    route /
    {
        methods = GET POST;           # Allowed HTTP methods
        root = ./www;                 # Document root
        indexFile = index.html;       # Default index file
        autoindex = on;               # Enable directory listing
        upload_path = ./uploads;      # Upload directory
    }
}
```

### Route Configuration Options

| Option | Description | Example |
|--------|-------------|---------|
| `methods` | Allowed HTTP methods | `methods = GET POST DELETE;` |
| `root` | Document root directory | `root = ./www;` |
| `indexFile` | Default index file | `indexFile = index.html;` |
| `autoindex` | Enable directory listing | `autoindex = on;` |
| `upload_path` | Directory for file uploads | `upload_path = ./uploads;` |
| `redirect` | URL redirect | `redirect = https://example.com;` |
| `cgi` | CGI handler configuration | `cgi = .py /usr/bin/python3;` |

### CGI Configuration

```nginx
route /cgi-bin
{
    methods = GET POST;
    root = ./cgi-bin;
    cgi = .py /usr/bin/python3;    # Python scripts
    cgi = .php php-cgi;             # PHP scripts
    cgi = .sh bash;                 # Shell scripts
    upload_path = ./uploads;
}
```

### Multiple Servers

You can configure multiple servers in a single configuration file:

```nginx
server
{
    host = 127.0.0.1;
    port = 8080;
    # ... configuration
}

server
{
    host = 127.0.0.1;
    port = 9090;
    # ... configuration
}
```

---

## 📁 Project Structure

```
Webserver/
├── Makefile                 # Build configuration
├── README.md                # This file
├── config/                  # Configuration files
│   ├── default.conf         # Default configuration
│   └── config.conf          # Custom configuration example
├── includes/                # Header files
│   ├── HttpServer.hpp       # Main server class
│   ├── Reactor.hpp          # Event loop (poll-based)
│   ├── Connection.hpp       # Client connection handling
│   ├── HttpRequest.hpp      # HTTP request parsing
│   ├── HttpResponse.hpp     # HTTP response building
│   ├── Router.hpp           # Request routing
│   ├── GetHandler.hpp       # GET request handler
│   ├── PostHandler.hpp      # POST request handler
│   ├── DeleteHandler.hpp    # DELETE request handler
│   ├── CgiHandler.hpp       # CGI script execution
│   ├── SessionManager.hpp   # Session management
│   └── ...                  # Other headers
├── src/                     # Source files
│   ├── main.cpp             # Entry point
│   ├── HttpServer.cpp       # Server implementation
│   ├── Reactor.cpp          # Event loop implementation
│   └── ...                  # Other source files
├── www/                     # Static web content
│   ├── index.html           # Main testing page
│   ├── login.html           # Login page
│   ├── test-*.html          # Various test pages
│   └── errp/                # Error pages
│       ├── 404.html
│       ├── 405.html
│       ├── 500.html
│       └── ...
├── cgi-bin/                 # CGI scripts
│   ├── test.py              # Python CGI example
│   ├── test.php             # PHP CGI example
│   └── ...
├── uploads/                 # Upload directory
└── session/                 # Session storage
```

---

## 📡 HTTP Methods

The server supports the following HTTP methods:

| Method | Description |
|--------|-------------|
| **GET** | Retrieve resources (files, directory listings) |
| **POST** | Submit data, file uploads |
| **DELETE** | Remove files from the server |

---

## 🔌 CGI Support

The server supports CGI (Common Gateway Interface) for dynamic content:

### Supported CGI Languages

- **Python** (`.py`) - Requires Python 3
- **PHP** (`.php`) - Requires php-cgi
- **Bash** (`.sh`) - Shell scripts
- **Perl** (`.perl`) - Requires Perl interpreter

### Example CGI Script (Python)

```python
#!/usr/bin/env python3
print("Content-Type: text/html")
print()
print("<html><body>")
print("<h1>Hello from CGI!</h1>")
print("</body></html>")
```

### CGI Environment Variables

The following environment variables are passed to CGI scripts:
- `REQUEST_METHOD`
- `QUERY_STRING`
- `CONTENT_TYPE`
- `CONTENT_LENGTH`
- `PATH_INFO`
- `SCRIPT_NAME`
- `SERVER_NAME`
- `SERVER_PORT`
- And more...

---

## 🍪 Session Management

The server includes built-in session management:

- Cookie-based session tracking
- Session ID generation
- Session data persistence
- Session timeout handling

---

## 🧪 Testing

The server includes several test pages accessible from the main index:

| Page | Description |
|------|-------------|
| `/test-get.html` | Test GET requests |
| `/test-post.html` | Test POST requests |
| `/test-delete.html` | Test DELETE requests |
| `/test-cgi.html` | Test CGI script execution |
| `/upload-test.html` | Test file uploads |
| `/login.html` | Test session/authentication |

### Testing with curl

```bash
# GET request
curl http://localhost:8080/

# POST request with data
curl -X POST -d "key=value" http://localhost:8080/

# File upload
curl -X POST -F "file=@path/to/file.txt" http://localhost:8080/uploads

# DELETE request
curl -X DELETE http://localhost:8080/uploads/file.txt
```

### Testing with a Browser

Navigate to `http://localhost:8080` to access the WebServ Testing Suite, which provides an interactive interface for testing all server features.

---

## 🛠️ Architecture

The server uses the **Reactor Pattern** for handling multiple concurrent connections:

1. **Reactor (Event Loop)**: Uses `poll()` to monitor file descriptors for I/O events
2. **HttpServer**: Manages server sockets and accepts new connections
3. **Connection**: Handles individual client connections
4. **RequestDispatcher**: Routes requests to appropriate handlers
5. **Handlers**: Specialized handlers for GET, POST, DELETE, and CGI

### Flow Diagram

```
Client Request → Reactor → HttpServer → Connection → RequestDispatcher
                                                           ↓
                                        ┌──────────────────┼──────────────────┐
                                        ↓                  ↓                  ↓
                                   GetHandler         PostHandler       DeleteHandler
                                                           ↓
                                                      CgiHandler
```

---

## 👥 Authors

- **Youness Tarhouani** - [1337 Student](https://github.com/Youness-Tr)
- **abdleali jabri** - [1337 Student](https://github.com/ajabrii)
- **baderdin aouragh** - [1337 Student](https://github.com/Badered10)

---

## 📄 License

This project was developed as part of the 1337 school 42Network/curriculum.

---

<p align="center">
  Made with ❤️ at 1337
</p>
