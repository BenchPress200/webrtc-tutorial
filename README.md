# <img src="https://static-00.iconduck.com/assets.00/webrtc-icon-2048x1987-45puxjf4.png" width="24"/> WebRTC Tutorial
This repository serves as a hands-on guide for WebRTC. It aims to clearly demonstrate how peer-to-peer connections are established and how video streams are exchanged using WebRTC.

> [!NOTE]
> You can find a more detailed explanation of the basic concepts of WebRTC and this tutorial’s code in my blog post. <br>
> [🔗 BenchPress200's Tech Blog](https://phellinus-linteus.tistory.com/3)

<br>

### 🌃 Preview
<img src="https://github.com/user-attachments/assets/fa93cf68-b764-4b95-9bc8-ee9a3e8de09d" width="600"/>

<br>

### 🔧 Stack
![Java](https://img.shields.io/badge/Java-007396?style=flat-square&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-6DB33F?style=flat-square&logo=spring-boot&logoColor=white)<br>
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)
![React](https://img.shields.io/badge/React-20232a?style=flat-square&logo=react&logoColor=61DAFB)
![Styled-Components](https://img.shields.io/badge/styled--components-DB7093?style=flat-square&logo=styled-components&logoColor=white)
![Zustand](https://img.shields.io/badge/Zustand-000000?style=flat-square)<br>
![WebSocket](https://img.shields.io/badge/WebSocket-010101?logo=websocket&logoColor=white&style=flat-square)
![WebRTC](https://img.shields.io/badge/WebRTC-333333?logo=webrtc&logoColor=white&style=flat-square)




<br><br>

## 📌 Before You Start

> Test Environment

This tutorial code can be tested in a local development environment.
To run the source code, you will need to have the following tools installed.
- VS Code
- Chrome
- node (v22.10.0)
- npm (v10.9.0)
- IntelliJ IDEA
- JDK 17

<br>

> WebRTC Communication Workflow

This tutorial handles not only signaling but all server communications via WebSocket.
To establish a peer-to-peer connection, a signaling process is required.
The steps involved in the signaling process to connection establishment are as follows.

<img src="https://github.com/user-attachments/assets/10f10d76-5519-4f07-880e-58b0c3b9b936" width="600"/>

1. Both Peer A and Peer B set up their own media streams(microphone and video) - `Peer A`, `Peer B`
2. One peer (Peer A) creates an SDP(offer) - `Peer A`
3. Peer A sets the generated SDP(offer) as its local description and sends it to Peer B - `Peer A`
4. Peer B receives the SDP(offer) and sets it as its remote description - `Peer B`
5. Peer B creates an SDP(answer) in response to the received SDP(offer)  - `Peer B`
6. Peer B sets its generated SDP(answer) as its local description and sends it to Peer A - `Peer B`
7. Peer A receives the SDP(answer) and sets it as its remote description - `Peer A`
8. Both peers exchange the ICE candidates they collected and add the remote candidates - `Peer A`, `Peer B`
    - Each peer begins gathering its own ICE candidates after completing SDP creation.
A peer can only add the other peer's candidates after setting the remote SDP
9. Once ICE candidate exchange is complete and a viable candidate pair is found, the connection is successfully established - `Peer A`, `Peer B`

<br>

> Key APIs required for the signaling process for video calling
- `new RTCPeerConnection()`
    - Creates a new WebRTC connection instance for peer-to-peer communication
- `peerConnection.ontrack = (event) => {}`
    - Triggered when a media track (audio or video) is received from the remote peer
- `peerConnection.onicecandidate = (event) => {}`
    - Called when a new ICE candidate is discovered
- `peerConnection.oniceconnectionstatechange = (event) => {}`
    - Monitors changes in the ICE connection state (e.g., connected, disconnected)
- `navigator.mediaDevices.getUserMedia()`
    - Requests access to the user's camera and microphone
- `peerConnection.createOffer()`
    - Creates an SDP offer to initiate a WebRTC connection with another peer
- `peerConnection.createAnswer()`
    - Creates an SDP answer in response to an offer from another peer
- `peerConnection.setLocalDescription()`
    - Sets the local peer’s SDP (offer or answer) for signaling
- `peerConnection.setRemoteDescription()`
    - Applies the received SDP from the remote peer
- `peerConnection.addIceCandidate()`
    - Adds an ICE candidate received from the remote peer to establish connectivity

<br>

> Custom hooks created to utilize these key APIs in accordance with the tutorial code

A custom WebRTC hook was implemented to establish a peer-to-peer connection through user interaction in the call page component.
It is located at [`/frontend/src/hooks/useWebRTC.ts`](https://github.com/BenchPress200/webrtc-tutorial/blob/main/frontend/src/hooks/useWebRTC.ts)

<br>

> Requirements for Production Deployment

When deploying a WebRTC-based feature to a production environment, there are several critical considerations beyond what is covered in this tutorial:

- Automated Signaling Flow<br>
In this tutorial, the signaling process is manually triggered by user interactions (e.g., button clicks). However, in a real-world service, signaling should be initiated and completed automatically—such as sending an offer and receiving an answer—without requiring direct user actions. This ensures a seamless connection experience.

- Ping-Pong Mechanism<br>
Since signaling is handled over WebSocket in this tutorial, production environments must account for possible disconnections caused by NATs, firewalls, load balancers, or web servers that enforce idle timeouts. If the WebSocket connection is terminated during signaling, the peer connection cannot be established. To resolve this, a ping-pong mechanism should be implemented to keep the signaling connection alive.

- TURN Server Deployment<br>
Unlike local environments, real-world peer networks are often restricted by NATs or firewalls, which can prevent peers from exchanging usable IP and port information. In such cases, a TURN (Traversal Using Relays around NAT) server acts as a relay to facilitate media transmission. Deploying a TURN server is essential to ensure reliable connectivity across various network conditions.




<br><br>

## 🏁 Getting Started
### 1. Clone Repository
```
git clone https://github.com/BenchPress200/webrtc-tutorial
```
Clone the tutorial repository to your local development environment.

<br>

### 2. Open the Frontend Code
<p align="left">
  <img src="https://github.com/user-attachments/assets/4ffd7a3d-1609-429a-a473-ecd0645e3d86" height="300"/>
  <img src="https://github.com/user-attachments/assets/8cf5d497-6f29-40d8-92cc-c3392af09d67" height="300"/>
</p>

Open `webrtc-tutorial/frontend` directory in VSCode to run the React app.

<br>


### 3. Install Required Packages
<img alt="스크린샷 2025-05-24 오후 8 11 56" src="https://github.com/user-attachments/assets/10eaec70-5101-4502-a673-eb304657296d" width="600"/><br>
```
npm install
```
In the VSCode window you just opened, open the terminal and run `npm install` to install the necessary packages.

<br>

### 4. Run the React App
<p align="left">
<img alt="스크린샷 2025-05-24 오후 8 13 43" src="https://github.com/user-attachments/assets/f61cbb80-fd37-4b22-bad9-c07f4e36403c" height="350"/>
<img alt="스크린샷 2025-05-24 오후 8 23 09" src="https://github.com/user-attachments/assets/e8599891-3d24-4fa0-866f-f0c5c57f5a51" height="350"/>
</p>


```
npm start
```
After the installation is complete, run `npm start` in the terminal to launch the React application.

<br>

### 5. Open the Backend Code in IntelliJ
> [!NOTE]
> If you don’t plan to look into the source code or test it directly, and just want to run the backend app without using an IDE like IntelliJ, you can simply open a terminal or PowerShell, navigate to `webrtc-tutorial/backend`, and run `./gradlew bootRun`. <br>
Then, you can skip the backend app setup steps below and start from step 8 🙂



<p align="left">
<img alt="스크린샷 2025-05-24 오후 8 17 44" src="https://github.com/user-attachments/assets/666e4ef2-58e8-48f8-86d1-3d29761d70b1" height="350"/>
<img alt="스크린샷 2025-05-24 오후 8 18 23" src="https://github.com/user-attachments/assets/4f7924c1-9835-445e-8ddb-c095b37d4912" width="300"/>
</p>

To run the Spring Boot app, open `webrtc-tutorial/backend` directory as a Gradle project in IntelliJ.

<br>

### 6. Configure Lombok
<p align="left">
    <img alt="스크린샷 2025-05-24 오후 8 25 38" src="https://github.com/user-attachments/assets/bb5862bf-2df6-4c4c-82e4-b1257dae11cc" height="350"/>
    <img alt="스크린샷 2025-05-24 오후 8 24 08" src="https://github.com/user-attachments/assets/3ad00cb0-8853-49a6-8dc3-d45716e5cda6" height="350"/>
</p>

Check the IntelliJ settings to ensure Lombok is working properly.

<br>

### 7. Run the Spring Boot App
<p align="left">
    <img alt="스크린샷 2025-05-24 오후 8 28 58" src="https://github.com/user-attachments/assets/d456ca7f-8b03-4147-80dd-ebef8c249ffc" width="500"/>
    <img alt="스크린샷 2025-05-24 오후 8 29 28" src="https://github.com/user-attachments/assets/c37f570d-3483-41f0-8148-697d98b98958" width="500"/>
</p>

Run the Spring Boot application.


<br>

### 8. Access the React App
<img alt="스크린샷 2025-05-24 오후 8 32 55" src="https://github.com/user-attachments/assets/e357a76c-65ec-4b81-b023-46a4e23d3697" width="800"/><br>

```
http://localhost:3000
```
Open your browser and launch two tabs with http://localhost:3000.

<br>

### 9. Allow Access to Microphone and Camera
<img alt="스크린샷 2025-05-24 오후 8 34 36" src="https://github.com/user-attachments/assets/cf748c71-4a29-46b6-b121-05b041d018be" width="600"/><br>
Allow the browser to access media devices (camera and microphone). If access is not granted, errors may occur.

<br>

### 10. Register Test Users
<img alt="스크린샷 2025-05-24 오후 8 34 36" src="https://github.com/user-attachments/assets/8119228b-228d-4e07-aba7-98fe3941c049" width="600"/><br>
Register as Patrick and SpongeBob, respectively.

<br>

### 11. Initiate a Call
<img alt="스크린샷 2025-05-24 오후 8 34 36" src="https://github.com/user-attachments/assets/536b54ae-4913-443d-a22f-0a67cfe99eb3" width="600"/><br>
Call SpongeBob, who then accepts the call. After that, both users are taken to the call page.


<br>


### 12. Signaling Process
<img alt="스크린샷 2025-05-24 오후 8 34 36" src="https://github.com/user-attachments/assets/ad29e739-b8f5-4cf0-9476-eee82cbf58f4" width="600"/><br>

1. `Patrick` and `SpongeBob` each set up their own video and microphone.
2. `SpongeBob`, who receives the call, waits for `Patrick’s` offer. `Patrick`, who initiates the call, creates and sends the offer first.
    - When `Patrick` creates the offer, his ICE candidates start to gather. As each candidate is collected, it is immediately sent to `SpongeBob`.
3. `SpongeBob` receives `Patrick’s` offer and sets it as the remote description.
    - Once the remote offer is set, `SpongeBob` is ready to add ICE candidates from `Patrick`. He adds any candidates that were buffered during the wait and continues to add new ones as they arrive.
4. `Patrick`, after sending the offer, waits for `SpongeBob’s` answer. `SpongeBob` creates and sends the answer to `Patrick`.
    - When `SpongeBob` creates the answer, his ICE candidates start to gather. As each candidate is collected, it is immediately sent to `Patrick`.
5. `Patrick` receives `SpongeBob’s` answer and sets it as the remote description.
    - Once the remote offer is set, `Patrick` is ready to add ICE candidates from `SpongeBob`. He adds any candidates that were buffered during the wait and continues to add new ones as they arrive.
6. Once both SDP messages have been exchanged and set, and ICE candidate exchange and connection are complete, the final step is marked as 'Done', and the video call becomes active.


<br> 


### 13. Start the Video Call
<img src="https://github.com/user-attachments/assets/836fadef-59ff-46fe-bab2-672cd2a52b1f" width="600"/><br>
Start the video call.


<br><br>

## ⚠️ Note on ICE Connection Behavior During Local Testing
In local test environments, you may observe that the callee (the user who receives the call) reaches an iceConnectionState === 'connected' status before the caller has finished setting the callee’s answer or adding ICE candidates.

This is not a bug, but a valid outcome under WebRTC’s behavior.

<br>

### ✅ Why this happens<br>
WebRTC uses Trickle ICE by default, where ICE candidates are exchanged after the offer/answer is set.

However, if a viable ICE candidate (e.g., a host candidate on the same LAN) is already included in the SDP, a peer connection may reach connected even if the other side has not completed their setup.

This can also occur more often in local networks (with no NAT or STUN/TURN needed), where direct connectivity is easily established.

<br>

### 🔁 What this means<br>
The callee can technically enter a connected state and begin sending media before the caller has finished applying the answer or ICE candidates.

Once the caller completes setting the callee’s answer and adds the pending candidates, the connection becomes fully established from both sides.

As a result, the call can proceed successfully even if the callee’s connection appears to be completed slightly earlier.

### 🛠️ Example Scenario<br>
1. Patrick (caller) creates an offer and starts gathering candidates.
2. SpongeBob (callee) receives the offer, sets it, creates an answer, and sends it back.
3. As SpongeBob gathers and sends his ICE candidates, he may already reach connected if a usable candidate pair is found (e.g., host-host).
4. Patrick, who hasn't yet set the answer or added candidates, appears “in progress.”
5. Once Patrick applies SpongeBob’s answer and adds his candidates, the connection is established from his side as well.

Both users can now proceed to a stable call state with no functional issues.


<br><br>

## 💥 Issue

If you encounter any issues or have questions about the tutorial code, I'd really appreciate it if you could open an issue using the "Issues" tab at the top of the repository page. I'll respond as soon as possible. To create an issue, please follow these steps:

1. Click the Issues tab
    <img width="991" alt="스크린샷 2025-05-23 오전 11 47 53" src="https://github.com/user-attachments/assets/1fa9a310-ea73-46e2-9736-4bf4370402f5" width="800"/>

3. Click New Issue
   <img width="974" alt="스크린샷 2025-05-23 오전 11 48 35" src="https://github.com/user-attachments/assets/3d873f75-84ce-49cc-b5f8-117c9d5af925" width="800"/>

5. Select the custom issue template
   <img width="903" alt="스크린샷 2025-05-23 오전 11 49 22" src="https://github.com/user-attachments/assets/562ce37c-1ba1-456a-b7b6-0cd5ec7dbe94" width="800"/>

7. Fill in a clear and relevant title, complete each section, and click Submit new issue
   <img width="825" alt="스크린샷 2025-05-23 오전 11 49 51" src="https://github.com/user-attachments/assets/179aa349-0ed3-406e-84f7-3b1bced81389" width="800"/>


Thank you for your contribution!

<br><br>
