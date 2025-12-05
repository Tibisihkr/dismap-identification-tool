# 🌀 Dismap - Asset Discovery and Identification Tool

<a href="https://github.com/zhzyker/dismap"><img alt="Go Version" src="https://img.shields.io/badge/golang-1.6+-9cf"></a>
<a href="https://github.com/zhzyker/dismap"><img alt="Version" src="https://img.shields.io/badge/dismap-0.3-ff69b4"></a>
<a href="https://github.com/zhzyker/dismap"><img alt="License" src="https://img.shields.io/badge/LICENSE-GPL-important"></a>
![GitHub Stars](https://img.shields.io/github/stars/zhzyker/dismap?color=success)
![GitHub Forks](https://img.shields.io/github/forks/zhzyker/dismap)
![Downloads](https://img.shields.io/github/downloads/zhzyker/dismap/total?color=blueviolet)

## Overview

Dismap is a powerful asset **discovery** and **identification** tool designed for security professionals. It rapidly identifies protocols and fingerprints across web, TCP, and UDP services, making it ideal for both internal and external network assessments.

### Key Features

- **Comprehensive Protocol Support**: Identifies TCP, UDP, and TLS protocols
- **Extensive Fingerprint Database**: Over **4,500 web fingerprint rules**
- **Multi-Target Detection**: Analyzes favicon, response body, headers, and more
- **Network Flexibility**: Works seamlessly on both internal and external networks
- **Fast Performance**: Concurrent scanning with customizable thread counts

### Use Cases

- **Red Team**: Quickly locate potential risk assets and attack surfaces
- **Blue Team**: Detect suspected vulnerable assets and security weaknesses
- **Asset Management**: Maintain an up-to-date inventory of network services

> **Note**: Version 0.3 introduces JSON output format. Integration with [vulmap](https://github.com/zhzyker/vulmap) vulnerability scanner will be available in vulmap >= 1.0.

---

## 🚀 Installation

Dismap is distributed as a standalone binary for Linux, MacOS, and Windows. Download the appropriate version from the [Releases](https://github.com/zhzyker/dismap/releases) page.

### Linux / MacOS

```bash
chmod +x dismap-0.3-linux-amd64
./dismap-0.3-linux-amd64 -h
```

### Windows

```cmd
dismap-0.3-windows-amd64.exe -h
```

![Dismap Screenshot](https://github.com/zhzyker/zhzyker/blob/main/dismap-images/dismap-0.3.png)

---

## 📖 Command-Line Options

| Option | Description |
|--------|-------------|
| `-f, --file` | Parse targets from a specified file for batch scanning |
| `-h, --help` | Display help information |
| `-i, --ip` | Specify network segment (e.g., `-i 192.168.1.0/24` or `-i 192.168.1.1-10`) |
| `-j, --json` | Save scan results in JSON format (e.g., `-j results.json`) |
| `-l, --level` | Set log level: 0=Fatal, 1=Error, 2=Info, 3=Warning (default), 4=Debug, 5=Verbose |
| `-m, --mode` | Specify protocol to scan (e.g., `-m mysql` or `-m http`) |
| `--nc` | Disable colored output |
| `--np` | Skip ICMP/PING host discovery |
| `-o, --output` | Save scan results to text file (default: `output.txt`) |
| `-p, --port` | Define custom port range (e.g., `-p 80,443` or `-p 1-65535`) |
| `--proxy` | Use proxy for scanning (supports HTTP/SOCKS5, e.g., `--proxy socks5://127.0.0.1:1080`) |
| `-t, --thread` | Set number of concurrent threads (default: 500) |
| `--timeout` | Configure response timeout in seconds (default: 5) |
| `--type` | Specify connection type (e.g., `--type tcp` or `--type udp`) |
| `-u, --uri` | Scan a specific target URI (e.g., `-u https://example.com`) |

---

## 💡 Usage Examples

### Basic Network Scan

```bash
./dismap -i 192.168.1.0/24
```

### Scan with Output Files

```bash
./dismap -i 192.168.1.0/24 -o results.txt -j results.json
```

### Skip Ping and Custom Timeout

```bash
./dismap -i 192.168.1.0/24 --np --timeout 10
```

### High-Performance Scan

```bash
./dismap -i 192.168.1.0/24 -t 1000
```

### Single Target Scan

```bash
./dismap -u https://github.com/zhzyker/dismap
```

### Service-Specific Scan

```bash
./dismap -u mysql://192.168.1.1:3306
```

### Full Port Scan

```bash
./dismap -i 192.168.1.0/24 -p 1-65535
```

---

## 💬 Community & Support

- **Bug Reports & Feature Requests**: [GitHub Issues](https://github.com)
- **Twitter**: [hzyker](https://twitter.com)

---

## 🔬 Rule Development Guide

The complete fingerprint rule base is defined in [`rule.go`](https://github.com/zhzyker/dismap/blob/main/configs/rule.go) as a structured format.

### Rule Structure

```go
Rule:
  Name: "rule_name"              // Define the rule name
  Type: "header|body|ico"        // Detection types (can be combined)
  Mode: "and|or"                 // Logical operator for Type evaluation
  Rule:
    InBody: "string"             // String that must exist in response body
    InHeader: "string"           // String that must exist in response header
    InIcoMd5: "md5_hash"         // MD5 hash of favicon.ico
  Http:
    ReqMethod: "GET|POST"        // HTTP request method
    ReqPath: "string"            // Custom request path
    ReqHeader: []string          // Custom HTTP headers
    ReqBody: "string"            // Custom POST request body
```

### Example 1: Simple Body Detection

Detect Apache Flink by checking for `<flink-root></flink-root>` in the response body:

```go
{"Apache Flink", "body", "", InStr{"(<flink-root></flink-root>)", "", ""}, ReqHttp{"", "", nil, ""}}
```

### Example 2: Custom Path with OR Logic

Detect Apache OFBiz by requesting a custom path and checking either header or body (supports regex):

```go
{"Apache OFBiz", "body|header", "or", InStr{"(Apache OFBiz|apache.ofbiz)", "(Set-Cookie: OFBiz.Visitor=(.*))", ""}, ReqHttp{"GET", "/myportal/control/main", nil, ""}}
```

### Rule Combination Guidelines

**Valid Combinations** ✅

- `"body|header|ico", "or"`
- `"body|header|ico", "or|and"`
- `"body|ico", "and"`

**Invalid Combinations** ❌

- `"body|body", "or"` (duplicate type)

**Alternative for Multiple Body Checks**

Instead of repeating types, use regex patterns:

```go
"body", "", InStr{"(string1|string2)", "", ""}
```

---

## 📝 License

This project is licensed under the GPL License. See the repository for details.
