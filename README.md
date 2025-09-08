# 🎥 Projeto Integrado: Virtualização de Webcam + Detecção com YOLOv8

> **Autor:** Professor Rodrigo D'ávila, Bacharelado em Sistemas de Informação e Pós-graduando em em Inteligência Artificial (I.a) e Machine Learning, Instrutor do SANAI-PA
> **Curso:** Técnico em Informática 
> **Descrição:** Este projeto une virtualização de hardware com inteligência artificial para criar um sistema de monitoramento inteligente. A webcam física do host Windows é transformada em uma câmera IP simulada dentro de uma VM Linux, com suporte a RTSP e ONVIF. Os vídeos capturados são processados com YOLOv8 para detecção de objetos.

---

## 📚 Objetivos

- Demonstrar como conectar hardware físico a sistemas operacionais virtualizados
- Ensinar captura de vídeo com Python e OpenCV
- Simular transmissão MJPEG e ONVIF
- Aplicar detecção de objetos com YOLOv8
- Integrar conceitos de virtualização, redes, IA e segurança

---

## ✅ Pré-requisitos

- Host Windows com VirtualBox + Extension Pack
- Máquina virtual Linux funcional
- Webcam integrada ou USB
- Python 3.8+ instalado na VM
- Modelo YOLOv8 (`yolov8s.pt`) disponível

---

## 🧰 Instalação

### 1. Instalar o Extension Pack do VirtualBox  
[https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)

### 2. Associar a webcam à VM

```bash
VBoxManage list webcams
VBoxManage controlvm "LINUX" webcam attach .0


3. Verificar no Linux
ls /dev/video*
ffplay /dev/video0



🐍 Captura de vídeo com Python + OpenCV
import cv2

cap = cv2.VideoCapture(0)
while True:
    ret, frame = cap.read()
    if not ret:
        break
    cv2.imshow("Webcam Linux VM", frame)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break
cap.release()
cv2.destroyAllWindows()



📡 Transmissão MJPEG com Flask (RTSP-like)
from flask import Flask, Response
import cv2

app = Flask(__name__)
camera = cv2.VideoCapture(0)

def generate_frames():
    while True:
        success, frame = camera.read()
        _, buffer = cv2.imencode('.jpg', frame)
        yield (b'--frame\r\nContent-Type: image/jpeg\r\n\r\n' + buffer.tobytes() + b'\r\n')

@app.route('/video_feed')
def video_feed():
    return Response(generate_frames(), mimetype='multipart/x-mixed-replace; boundary=frame')

app.run(host='0.0.0.0', port=8080)



📷 Simulação ONVIF com Python
from onvif import ONVIFCamera

cam = ONVIFCamera('192.168.0.10', 80, 'admin', 'admin', '/etc/onvif/wsdl')
info = cam.devicemgmt.GetDeviceInformation()
print("Fabricante:", info.Manufacturer)
print("Modelo:", info.Model)



🎯 Detecção de Objetos com YOLOv8
Instalar Ultralytics
pip install ultralytics


Script de detecção
from ultralytics import YOLO
import os

pasta_videos = r"C:\Users\USER\Videos\VideosRecebidos"
pasta_saida = r"C:\Users\USER\Videos\VideosDetectados"
os.makedirs(pasta_saida, exist_ok=True)

modelo = YOLO("yolov8s.pt")

for nome_arquivo in os.listdir(pasta_videos):
    if nome_arquivo.lower().endswith((".mp4", ".avi", ".mov")):
        caminho_video = os.path.join(pasta_videos, nome_arquivo)
        nome_base = os.path.splitext(nome_arquivo)[0]
        nome_saida = f"detectado_{nome_base}.mp4"
        caminho_video_saida = os.path.join(pasta_saida, nome_saida)

        resultados = modelo.predict(
            source=caminho_video,
            save=True,
            save_txt=False,
            conf=0.5
        )

        for r in resultados:
            if hasattr(r, 'save_path') and r.save_path.endswith(".mp4"):
                os.rename(r.save_path, caminho_video_saida)
                break



📁 Estrutura do Projeto
virtualbox-webcam-yolo/
├── README.md
├── requirements.txt
├── webcam_capture.py
├── rtsp_server.py
├── onvif_simulator.py
├── yolo_detector.py
├── VideosRecebidos/
├── VideosDetectados/



📋 requirements.txt
flask
opencv-python
numpy
onvif-zeep
ultralytics



🧠 Possibilidades Futuras
- 🔐 Autenticação ONVIF
- 📡 RTSP real com GStreamer ou aiortc
- 🧠 IA embarcada: OCR, reconhecimento facial, placas
- 📊 Dashboard com estatísticas de detecção
- 🐳 Dockerização do sistema completo

🎓 Aplicações Educacionais
Este projeto é ideal para:
- Aulas de redes, sistemas operacionais e IA
- Demonstrações práticas de integração entre software e hardware
- Projetos de TCC ou iniciação científica
- Simulações de sistemas de segurança e monitoramento

