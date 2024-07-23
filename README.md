# CHAMA - An HTTP Server Written In Rust

------

CHAMA can:

🗿 Handle `HTTP`, `GET`, and `POST` requests. 

🗿 Provide several endpoints such as `/`, `/user-agent`, `/files/{filename}`, and `/echo/{str}`.

🗿 Manage concurrent connections. 

🗿 Support gzip compression for responses if requested by the client.

<br>

<p align="center">
<img align="center" src="https://media1.giphy.com/media/OMcUAlCUQOcNnXdU9j/giphy.webp?cid=ecf05e47cjpu4l5jqqoc8n4gci76350mauyjx3xpfjew4qzj&ep=v1_gifs_search&rid=giphy.webp&ct=g"/>
</p>

<br>

## How CHAMA Works
```mermaid
graph TD
    A[Start] --> B[Parse Command Line Arguments]
    B --> C{Directory Provided?}
    C -->|Yes| D[Use Provided Directory]
    C -->|No| E[Use Current Directory]
    D --> F[Create TcpListener]
    E --> F
    F --> G[Listen for Incoming Connections]
    G --> H[Accept Connection]
    H --> I[Spawn New Thread]
    I --> J[Read Request]
    J --> K[Parse Request]
    K --> L{Determine Request Type}
    L -->|GET| M[Handle GET Request]
    L -->|POST| N[Handle POST Request]
    L -->|Other| O[Send 405 Method Not Allowed]
    M --> P{Determine GET Path}
    P -->|Root| Q[Send 200 OK]
    P -->|/user-agent| R[Send User-Agent]
    P -->|/files/*| S[Send File Contents]
    P -->|/echo/*| T[Echo Path Content]
    P -->|Other| U[Send 404 Not Found]
    N --> V{Is /files/* Path?}
    V -->|Yes| W[Write File]
    V -->|No| X[Send 405 Method Not Allowed]
    W --> Y[Send 201 Created]

    subgraph "Memory Management"
    Z[Stack: Local Variables]
    AA[Heap: Dynamic Allocations]
    AB[File System: Stored Files]
    end
    
    subgraph "Input/Output"
    AC[Input: HTTP Requests]
    AD[Output: HTTP Responses]
    AE[File I/O: Read/Write]
    end

    

    subgraph "Key Components"
    AF[TcpListener]
    AG[TcpStream]
    AH[File Operations]
    AI[GzEncoder]
    end
    
    subgraph "Concurrency Model"
    AJ[Thread per Connection]
    end
```
<br>

## Endpoints

The server provides several endpoints. A GET request to `/` returns a `200 OK` response with an empty body. A GET request to `/user-agent` returns the `User-Agent` header value sent by the client. For the `/files/{filename}` endpoint, a GET request returns the content of the requested file if it exists, otherwise it returns a `404 Not Found` response. The `/echo/{str}` endpoint echoes the string provided in the URL. The server also supports POST requests to the `/files/{filename}` endpoint, saving the request body as a file with the given filename.

<br>

## CHAMA In Action

 GET request to the root URL:
```sh
curl -v http://localhost:4221/
```
Testing the `/user-agent` endpoint:
```sh
curl -v -H "User-Agent: custom-agent" http://localhost:4221/user-agent
```
Requesting files:
```sh
echo "Hello, World!" > /tmp/hello.txt
curl -v http://localhost:4221/files/hello.txt
```
Echo endpoint:
```sh
curl -v http://localhost:4221/echo/hello
```
Saving data to a file via a POST request:
```sh
curl -v -X POST --data "This is a test file." http://localhost:4221/files/test.txt
```

<br>

## Make It Your Own

To use CHAMA, your system must have [Rust](https://www.rust-lang.org/tools/install) installed. 

First, clone the repository with the command:
```sh
git clone https://github.com/V3D4NTH/CHAMA
cd CHAMA
```
Build the project using:
```sh
cargo build --release
```
You can then run the server with:
```sh
./target/release/http-server-starter-rust --directory /path/to/serve
```


