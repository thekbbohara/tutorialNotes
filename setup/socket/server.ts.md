---
id: server.ts
aliases: []
tags: []
---

|--------------|                   |--------------|
|    Client    |===================|    Server    |
|--------------|                   |--------------|
      |                                    |
      | connection and disconnect          |
      |                                    |
      |                                    |
      |              connect               |
      |----------------------------------->|
      |                                    |
      |                                   Disconnect
      |                                    |----|
      |                                    |<---|
      |                                    |
|--------------|                   |--------------|
|    Client    |===================|    Server    |
|--------------|                   |--------------|

```bash
./src/server.ts
```
```ts
import {createServer  } from "http" // to create http server
import { Server } from 'socket.io' // to create web socket server
import express from "express"

const PORT = process.env.PORT || 8000

const app = express()

const server = createServer(app)

const io = new Server(server)

io.on("connection",socket=>{
  console.log('a user connected:', socket?.id)
  socket.on("disconnect",()=>{
    console.log("socket disconnected:",socket?.id)
  })
})

server.listen(PORT,()=>{
  console.log(`Serverlistening on port:${PORT}`)
})
```
