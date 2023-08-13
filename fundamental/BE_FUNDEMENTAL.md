# Backend fundamental

## 1. Communication model
### 1.1 Request Response

- Client sends a Request
- Server parses the Request
- Server processes the Request
- Server sends a Response
- Client parses the Response and consume

Anatomy of a Request / Response
- A request structure is defined by both client and server
- Request has a boundary
- Defined by a protocol and message format
- Same for the response
- E.g. HTTP Request

WHen not to used Request response model:
- Chat application 
- Notification service
- Client disconnect
- Long request: can be use async request

### 1.2 Sync vs Async

Synchronous I/O:
- Caller sends a request and blocks
- Caller cannot execute any code meanwhile
- Receiver responds, Caller unblocks
- Caller and Receiver are in "sync"

Asynchronous I/O:
- Caller send request and do other task, it continue to work other task until get a response.
- Caller either:
  - Checks if the response is ready (epoll).
  - Receiver calls back when it's done (io_uring)
  - Spins up a new thread that blocks

### 1.3 Push
- Good case of push:
  - Notification service
  - Chat application

What is Push?
- Client connects to a server
- Server sends data to the client
- Client doesn’t have to request anything
- Protocol must be bidirectional
- RabbitMQ use push, Kafka use poll

Pros:
- Real time

Cons:
- Client must be online
- Client may not able to handle
- Polling is better for light client (iot device, mobile, ....)

### 1.4 Short Polling
- When use short polling instead of request, response:
  - When the process is very long. Example: youtube use an uploadId to mark that you are uploading and create a polling connection to check if upload is done.

What is Short Polling?
- Client sends a request
- Server responds immediately with a unique id
- Server continues to process the request
- Client uses that unique id to check for status
- Multiple “short” request response as poll

Pros:
- Simple 
- Good for long running request
- Client can disconnect

Cons:
- Hard to scale, just too many request.
- Waste bandwidth. 

### 1.5 Long Polling

