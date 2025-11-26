# FileSync

A lightweight, multi-client FTP server implementation in C that enables secure file transfer between clients and server over TCP/IP networks.

## Overview

FileSync is a custom File Transfer Protocol server built from scratch using TCP sockets and system-level C programming. The server supports multiple concurrent client connections through fork-based process management, allowing simultaneous file operations across different users.

## Features

- **Multi-Client Support**: Handles multiple simultaneous client connections using fork-based concurrency
- **User Authentication**: Credential-based authentication system with username/password verification
- **File Operations**:
  - **Upload**: Transfer files from client to server
  - **Download**: Retrieve files from server to client
  - **Preview**: View first 1KB of any file without downloading
  - **List**: Display all files in user's directory
  - **Remove**: Delete files from server
- **User Isolation**: Each user has a dedicated directory for secure file storage
- **Session Management**: Clean connection handling with proper resource cleanup

## Technical Architecture

- **Language**: C
- **Protocol**: TCP/IP
- **Concurrency Model**: Fork-based process spawning for each client connection
- **Platform**: Linux (Debian-based distributions)
- **Authentication**: File-based credential storage with format validation

## Prerequisites

- GCC compiler
- Linux operating system (Debian-based)
- Make utility
- Basic understanding of TCP/IP networking

## Installation & Setup

### Server Setup

1. **Organize files** - Place `server.c`, `common.c`, and `Makefile` in a directory

2. **Build the server**:
```bash
   make
```

3. **Create authentication file** - Create `id_passwd.txt` with user credentials:
```
   username1:password1
   username2:password2
```

4. **Create user directories** - For each user in `id_passwd.txt`, create an empty directory:
```bash
   mkdir username1
   mkdir username2
```

5. **Start the server**:
```bash
   ./server.out [PORT_NUMBER]
```
   Example:
```bash
   ./server.out 8000
```

### Client Setup

1. **Organize files** - Place `client.c`, `common.c`, and `Makefile` in a directory

2. **Build the client**:
```bash
   make
```

3. **Connect to server**:
```bash
   ./client.out [SERVER_IP] [SERVER_PORT]
```
   Example:
```bash
   ./client.out 192.168.1.100 8000
```

## Usage

After connecting, the client presents an interactive menu:
```
Commands:
  u - Upload file from local to server
  d - Download file from server to local
  v - View first 1KB of a file on server
  p - Print contents of your server directory
  r - Remove a file from server
  q - Quit session
```

### Example Session
```bash
$ ./client.out 192.168.1.100 8000

Connected to Server@192.168.1.100|8000 ...

Enter userName :$$ john
Enter password :$$ pass123
User Name and password validated

Enter appropriate command :$$ u
Enter path of file(on-client) to upload :$$ /home/john/document.pdf
Enter name to store the file (on-server) :$$ my_document.pdf
FILE SIZE to be transferred is 524288B
Finished Sending file from Client side

Enter appropriate command :$$ p
The contents of your server folder are
        my_document.pdf
Thats all ....

Enter appropriate command :$$ q
ByeBye..session_ended..
```

## Architecture Details

### Connection Flow
```
Client                          Server
  |                               |
  |-------- Connect ------------->|
  |<------- Accept ---------------|
  |                               |--- fork() new process
  |                               |
  |------ Send Username --------->|
  |------ Send Password --------->|
  |<--- Authentication Result ----|
  |                               |
  |------ Command (u/d/v/p/r) --->|
  |<----- File Data / Status -----|
  |                               |
  |-------- Disconnect ---------->|
```

### File Transfer Protocol
- Buffer size: 255 bytes for control messages, 1024 bytes for file preview
- File size transmitted before data transfer
- Binary mode for file operations
- Acknowledgment-based communication

## Security Considerations

**Current Implementation:**
- Basic username/password authentication
- Per-user directory isolation
- Credential storage in plain text file

**Note**: This is an educational implementation. Production systems should use:
- Encrypted password storage (hashing with salt)
- SSL/TLS for encrypted transmission
- More robust authentication mechanisms
- Input validation and sanitization

## Known Limitations

- Non-standard FTP protocol (custom implementation)
- Plain text credential storage
- No encryption for data in transit
- Linux-only compatibility
- Fixed buffer sizes
- No support for nested directories

## Error Handling

The application includes comprehensive error checking for:
- Socket creation and binding failures
- Connection errors
- File I/O operations
- Authentication failures
- Invalid commands
- Network transmission errors

## Clean Up

To remove compiled files:
```bash
make clear
```

## Future Enhancements

- [ ] SSL/TLS encryption for secure transmission
- [ ] Encrypted password storage
- [ ] Support for nested directory structures
- [ ] Resume interrupted file transfers
- [ ] Compression for large files
- [ ] Cross-platform compatibility (Windows, macOS)
- [ ] GUI client application
- [ ] Rate limiting and bandwidth management

## Technical Highlights

- **Low-level socket programming** with direct system calls
- **Process management** using fork() for concurrency
- **Binary file handling** with proper buffer management
- **Network byte order conversion** for cross-platform compatibility
- **Signal handling** for graceful shutdown (SIGINT)

## License

This project is available for educational purposes.

## Author

Chethan - Master's in Information Systems, Northeastern University

## Acknowledgments

Built as a learning project to understand:
- TCP/IP socket programming
- Client-server architecture
- Process-based concurrency
- File I/O operations in C
- Network protocol design