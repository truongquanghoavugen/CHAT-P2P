# UDM_09 — Chat P2P

Ứng dụng chat P2P chạy trực tiếp qua TCP/IP trong mạng LAN, không có server trung tâm.

## Chức năng

- GUI toàn bộ chức năng.
- Mỗi máy đồng thời là TCP server + TCP client.
- Kết nối 2–3 máy trong cùng LAN.
- Peer List: Online/Offline, IP/Port, Peer ID.
- Chat realtime.
- Timestamp.
- Reply message.
- Forward message sang Peer khác.
- Emoji.
- UTF-8/tiếng Việt.
- JSON Message Protocol.
- TCP message framing bằng `\\n` để tránh lỗi dính/tách message.
- Thread nhận dữ liệu để GUI không bị treo.

## Cấu trúc

```text
UDM09_P2P_CHAT/
├── main.py
├── network/
│   ├── __init__.py
│   └── p2p_connection.py
├── protocol/
│   ├── __init__.py
│   └── message_protocol.py
├── gui/
│   ├── __init__.py
│   ├── chat_gui.py
│   ├── peer_manager.py
│   ├── reply_emoji.py
│   └── forward.py
├── assets/avatars/
└── requirements.txt
```

## Chạy

```powershell
cd UDM09_P2P_CHAT
python main.py
```

Lần chạy đầu sẽ hỏi:

1. Tên hiển thị.
2. TCP Port, mặc định `5000`.

Sau khi mở app, giao diện hiển thị IP LAN của máy.

## Demo 2 máy

### Máy A

- Chạy app.
- Ví dụ IP: `192.168.1.10`, Port `5000`.

### Máy B

- Chạy app.
- Ví dụ IP: `192.168.1.11`, Port `5000`.
- Trên máy B nhập `192.168.1.10` + `5000` rồi Connect.

Hoặc máy A có thể Connect tới máy B. Chỉ cần một phía chủ động Connect, sau đó cả hai phía sẽ thấy Peer online.

## Demo 3 máy

Ví dụ:

```text
A = 192.168.1.10:5000
B = 192.168.1.11:5000
C = 192.168.1.12:5000
```

Kết nối:

```text
A -> B
A -> C
B -> C
```

Sau đó demo:

- A chat B.
- B reply A.
- A forward message cho C.
- C gửi emoji cho A.
- Tắt B để kiểm tra Offline.
- Mở B lại và Connect để kiểm tra Online.

## Windows Firewall

Nếu 2 máy không kết nối được, cho phép Python qua Windows Defender Firewall trên mạng Private hoặc tạo inbound rule cho TCP port 5000.

Kiểm tra port từ máy khác bằng PowerShell:

```powershell
Test-NetConnection 192.168.1.10 -Port 5000
```

Nếu `TcpTestSucceeded : True` thì port đang truy cập được.

## Phân chia thành viên

- Võ Anh Duy — Connection Layer / `network/p2p_connection.py`
- Trương Quang Hòa — Message Protocol / `protocol/message_protocol.py`
- Nguyễn Đông Nam — Main Chat GUI / `gui/chat_gui.py`
- Lê Gia Huy — Peer Management / `gui/peer_manager.py`
- Trần Thị Đời Trang — Reply + Emoji / `gui/reply_emoji.py`
- Nguyễn Duy Nhất — Forward / `gui/forward.py`

## Gợi ý thuyết trình

Đi theo luồng:

`GUI -> MessageProtocol -> P2PConnection -> TCP -> Peer -> GUI`

Nhấn mạnh: **không có server trung tâm**; mỗi máy đều vừa Listen vừa Connect.
