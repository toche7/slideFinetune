---

## Run vLLM on Colab

**Goal:** เปิด LLM API Server บน Colab GPU และให้เครื่องภายนอกเรียกผ่าน ngrok

```text
Client → ngrok → Colab → vLLM → GPU → LLM
```

---

## 1) Enable GPU

```text
Runtime → Change runtime type → GPU
```

ตรวจสอบ GPU:

```python
!nvidia-smi
```

ถ้า Colab เป็น **A100 / L4 / T4** แล้ว vLLM จะใช้ GPU นั้นอัตโนมัติ

---

## 2) Install Packages

```python
!pip install -U vllm pyngrok
```

ถ้าเจอปัญหา `torch` กับ `torchaudio` CUDA ไม่ตรงกัน:

```python
!pip uninstall -y torchaudio
```

---

## 3) Start vLLM Server

รันแบบ background เพื่อให้ใช้ cell อื่นต่อได้:

```python
!nohup vllm serve Qwen/Qwen3-8B \
  --host 0.0.0.0 \
  --port 8000 \
  --api-key demo-key \
  > /content/vllm.log 2>&1 &
```

ดู log:

```python
!tail /content/vllm.log
```

---

## 4) Test Local API

```bash
%%bash
curl http://localhost:8000/v1/chat/completions \
  -H "Authorization: Bearer demo-key" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "Qwen/Qwen3-8B",
    "messages": [
      {"role": "user", "content": "Hello from Colab"}
    ]
  }'
```

ถ้าได้ JSON response = server พร้อมใช้งาน

---

## 5) Open Public URL with ngrok

โหลด token จาก Colab Secrets:

```python
from google.colab import userdata
from pyngrok import ngrok

ngrok.set_auth_token(userdata.get("NGROK_AUTHTOKEN"))
public_url = ngrok.connect(8000)
print(public_url)
```

ngrok ทำหน้าที่เป็น tunnel เท่านั้น — inference ยังเกิดบน GPU ของ Colab

---

## 6) Call from Outside

```bash
curl https://YOUR-NGROK-URL/v1/chat/completions \
  -H "Authorization: Bearer demo-key" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "Qwen/Qwen3-8B",
    "messages": [
      {"role": "user", "content": "Hello from outside Colab"}
    ]
  }'
```

ใช้กับเครื่องนักเรียน, Postman, Python, หรือ n8n ได้

---

## 7) Streaming Mode

```bash
curl -sS -N https://YOUR-NGROK-URL/v1/chat/completions \
  -H "Authorization: Bearer demo-key" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "Qwen/Qwen3-8B",
    "messages": [
      {"role": "user", "content": "Hello via streaming"}
    ],
    "stream": true
  }'
```

`-N` ช่วยให้ curl แสดง token แบบไม่ buffer

---

## # Key Takeaways

- **vLLM** = OpenAI-compatible LLM server
- **Colab GPU** = ตัวประมวลผลจริง เช่น A100
- **ngrok** = public tunnel เข้ามาที่ Colab
- **1 vLLM instance ≈ 1 main model**
- เหมาะสำหรับ **teaching / demo / prototype** ไม่ใช่ production
