# 2b IMPLEMENTATION OF SLIDING WINDOW PROTOCOL.

### Developed by: Anbuselvan.S
### Register No: 212223240008

## **Aim:**
To implement the **Sliding Window Protocol** using Python, allowing a client to send multiple frames to a server, receive acknowledgment (ACK) for each frame, and ensure reliable data transmission by resending unacknowledged frames.

## **Procedure:**

1. **Create the Client (Sender):** 
   - Prompt user for frame size, define window size, and create a socket for communication.
   - Send frames within the window to the server and wait for ACK.
   
2. **Create the Server (Receiver):**
   - The server receives frames and sends ACK for each frame.
   - Check for in-order frames and ignore out-of-order ones.
   
3. **Sliding Window Mechanism:** 
   - The client maintains a window of sent but unacknowledged frames.
   - If ACK is received, slide the window forward, else retransmit unacknowledged frames.

4. **Ensure Reliable Transmission:**
   - Handle timeout for lost ACKs and resend frames until all frames are successfully acknowledged.
 
## PROGRAM:

### Server:
```py
import socket

def server_program():
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    server_socket.bind(('localhost', 12345))
    
    print("Server is listening...")
    expected_frame = 0

    while True:
        # Receive frame
        frame, addr = server_socket.recvfrom(1024)
        frame_number = int(frame.decode().split()[1])  # Extract frame number
        
        if frame_number == expected_frame:
            print(f"Received: {frame.decode()}")
            ack = str(frame_number).encode()
            server_socket.sendto(ack, addr)
            print(f"Sent ACK for frame {frame_number}")
            expected_frame += 1
        else:
            print(f"Out of order frame: {frame.decode()}, expecting frame {expected_frame}")

if __name__ == '__main__':
    server_program()
```

### Client:
```py
import socket
import time

def client_program():
    server_address = ('localhost', 12345)
    window_size = 4  # Number of frames the sender can send before waiting for ACK
    total_frames = 10  # Total number of frames to be sent
    
    # Create UDP socket
    client_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    
    sent_frames = 0
    acked_frames = 0
    
    while acked_frames < total_frames:
        # Send frames within the window
        while sent_frames < total_frames and sent_frames - acked_frames < window_size:
            frame = f"Frame {sent_frames}".encode()
            client_socket.sendto(frame, server_address)
            print(f"Sent: {frame.decode()}")
            sent_frames += 1
            time.sleep(1)  # Simulate time delay in sending frames
        
        # Receive acknowledgment
        try:
            client_socket.settimeout(2)
            ack, addr = client_socket.recvfrom(1024)
            ack = int(ack.decode())
            print(f"Received ACK for frame: {ack}")
            acked_frames = ack + 1
        except socket.timeout:
            print("Timeout! Resending unacknowledged frames...")

    print("All frames sent and acknowledged.")
    client_socket.close()

if __name__ == '__main__':
    client_program()
```

## OUPUT:

### Server:
![image](https://github.com/user-attachments/assets/f9463c1e-4bff-4878-aca5-464e703fd0cd)

### client:
![image](https://github.com/user-attachments/assets/71fa6ee9-70c8-42a6-aa94-36893cbefc26)

## RESULT
Thus, python program to perform stop and wait protocol was successfully executed
