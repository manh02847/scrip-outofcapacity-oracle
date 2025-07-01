
# Oracle Auto-VPS Creator (Out Of Capacity Bypass Bot)

Script Python này giúp tự động kiểm tra và tạo VPS (VM.Standard.A1.Flex) Oracle Ampere A1 khi có slot trống, đồng thời gửi thông báo qua Telegram.

---

## 📌 Yêu cầu

- Python 3.x
- Tài khoản Oracle Cloud
- Tài khoản Telegram (để nhận thông báo)
- SSH Key
- Cấu hình tài nguyên: 4 OCPU - 24GB RAM

---

## 🧾 Các bước cấu hình

### 1. Tạo API Key cho Oracle Cloud

- Truy cập Oracle Console: [https://cloud.oracle.com/](https://cloud.oracle.com/)
- Vào **Identity > Users > [Tên của bạn] > API Keys**
- Nhấn **Add API Key**
- Chọn **Generate API Key Pair**
- Tải về và lưu lại `private key` (dạng `.pem`)

Trong `config` file cần các thông tin sau:

```ini
[DEFAULT]
user=ocid1.user.oc1..xxxxx
fingerprint=xx:xx:xx:xx:xx:xx
tenancy=ocid1.tenancy.oc1..xxxxxx
region=eu-paris-1
key_file=/path/to/oci_api_key.pem
```

> 🔐 **Lưu ý:** Không chia sẻ `key_file` và `ocid` của bạn!

---

### 2. Tạo thông tin mạng

Bạn cần:

- **compartment_id**
- **subnet_id**
- **availability_domains**: Copy từ OCI Console.

---


### 3. Tạo SSH Key bằng PuTTYgen (dễ cho Windows)

1. Tải về [PuTTYgen](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
2. Mở PuTTYgen → Chọn loại key là **RSA**, số bit là **4096**
3. Nhấn **Generate** rồi di chuột để tạo key
4. Copy phần `Public key` dán vào `ssh_authorized_keys`
5. Nhấn **Save private key** để lưu `.ppk` dùng cho kết nối SSH

> 🔁 Nếu cần key dạng OpenSSH (dùng cho code), vào menu Conversions → **Export OpenSSH key**

---
### 4. Lấy URL và Payload API tạo instance (qua F12 - cURL bash)

1. Truy cập trang tạo VPS: https://cloud.oracle.com/compute/instances
2. Nhấn **F12** để mở DevTools → tab **Network**
3. Nhấn **Create** để tạo VPS như bình thường
4. Trong danh sách request, chọn dòng có `instance` (màu đỏ nếu bị lỗi)
5. Nhấn chuột phải → **Copy > Copy as cURL (bash)**
6. Dán ra để lấy đầy đủ URL, Header và Payload bạn cần, ví dụ:

```bash
curl 'https://iaas.eu-paris-1.oraclecloud.com/20160918/instances/'   -X POST   -H 'Authorization: Bearer ...'   -H 'Content-Type: application/json'   --data-raw '{
    "availabilityDomain": "EU-PARIS-1-AD-1",
    "compartmentId": "ocid1.compartment.oc1...",
    "shape": "VM.Standard.A1.Flex",
    "shapeConfig": {
      "ocpus": 4,
      "memoryInGBs": 24
    },
    "sourceDetails": {
      "sourceType": "image",
      "imageId": "ocid1.image.oc1..."
    },
    "metadata": {
      "ssh_authorized_keys": "ssh-rsa AAAAB3Nza..."
    }
  }'
```

> ✳️ Dùng đoạn JSON ở phần `--data-raw` làm mẫu để viết lại cho script Python nếu cần.

---


### 5. Cấu hình các thông tin sau:
Trong file `amd.py` hoặc `arm.py` thay thông tin từ Url (bash) đã copy vào
Dán vào notepad nhấn CTRL + F nhập 'availabilityDomains' rồi thay vào, các mục khác tương tự.
```python
availabilityDomains = ["yvaO:EU-PARIS-1-AD-1"]
displayName = 'instance-2025...'
compartmentId = "ocid1.tenancy.oc1..."
subnetId = "ocid1.subnet.oc1..."
ssh_authorized_keys = "ssh-rsa AAAAB3Nza..."

ocpus = 4
memory_in_gbs = 24

bot_token = "your_telegram_bot_token"
uid = "your_telegram_chat_id"
```

---

## ▶️ Chạy bot

# Windoe
```bash
Python Download Link  :-  https://www.python.org/

GitHub Login  :-  https://github.com/login

Opening Command Prompt  :-  cmd

Install Python Packages  :-  pip install -r requirements.txt

Script Starting  :-  python bot.py
```

Muốn chạy ẩn trong nền (không tắt khi logout):

```bash
screen -S oracle_bot
python3 bot.py
# Ctrl + A, rồi nhấn D để thoát khỏi screen
```

---

## 📬 Kết quả

- Khi có slot, script sẽ tự động tạo VPS.
- Bạn sẽ nhận được thông báo trên Telegram với chi tiết instance và region.

---

## 🧯 Ghi chú

- Kiểm tra kỹ quota và giới hạn Oracle để tránh lỗi.
- Nếu script báo lỗi `Out of capacity`, bot sẽ tự retry.

---

## 🧑‍💻 Tác giả

- Cập nhật và đóng góp tại: (https://github.com/manh02847/scrip-outofcapacity-oracle)
