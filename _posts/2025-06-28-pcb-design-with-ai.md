---
layout: post
title: "PCB Design with AI"
date: 2025-06-28
categories: [engineering]
tags: [ai-tools, design, hardware]
---

Hari ini saya dipertemukan dengan [Flux.AI](https://www.flux.ai/) lewat google saat searching hal lain tetapi menemukan itu. 
Seketika saya langsung teringat bahwa saya pernah sekali buat PCB dengan teknik fotocopy lalu di setrika.
Hanya sebatas itu sampai soldering dan programming. tetapi saya masih penasaran sampai sekarang saya belum bisa Desain PCB-nya.

Lalu dari mana saya bisa mulai belajar? Jika saya memang pemula dan apa endingnya?

Hal ini sejalan waktu saya eksplorasi terkait HaiMex atau sebuah AI Asisten yang pikiran itu dimulai dari baymax lalu,
terlihat kembali anki vector dan mulai lah mencari DIY-nya sampai akhirnya ditemukan semua part di dalam vector.

Saya sudah coba bantu dengan AI untuk design diberitahu semua hardware yang dibutuhkan.

Ini dia,

> Implementasi Baymax di Raspberry Pi 3 - Roadmap Praktis

### 1. Penilaian Keterbatasan & Solusi Raspberry Pi 3

##### Keterbatasan Hardware
- **CPU**: ARM Cortex-A53 quad-core 1.2GHz (terbatas untuk AI processing)
- **RAM**: 1GB (kurang untuk model AI besar)
- **GPU**: VideoCore IV (basic graphics processing)
- **Storage**: MicroSD (kecepatan I/O terbatas)

##### Strategi Optimasi dengan API Integration
- **API-First Approach**: Gunakan API untuk AI processing (OpenAI, Google AI, dll)
- **Local Sensors**: RPi3 handle sensor data collection dan basic logic
- **Smart Caching**: Cache responses untuk reduce API calls
- **Offline Fallback**: Basic responses tanpa internet connection

### 2. Arsitektur Sistem yang Realistis

##### Tier 1: Local Processing (Raspberry Pi 3)
```python
# Core Functions di RPi3
- Sensor data collection & environmental monitoring
- Voice capture & audio processing
- Basic conversation flow & personality
- Context data preparation
- Emergency protocols & safety features
- Response caching untuk speed
```

##### Tier 2: API Processing (Cloud AI Services)
```python
# AI processing via API calls
- Advanced NLP & conversation AI (OpenAI GPT, Claude API)
- Complex reasoning & decision making
- Personality & context-aware responses
- Learning & adaptation algorithms
- Health advice & recommendations
```

##### Tier 3: Integration Layer
```python
# Smart coordination between local & cloud
- Request optimization & batching
- Context enrichment with sensor data
- Response personalization
- Offline fallback handling
```

### 3. Implementasi Fase-per-Fase

##### FASE 1: Basic Baymax (Minggu 1-4)
**Target**: Voice assistant dasar dengan personality

###### Hardware yang Dibutuhkan:
- Raspberry Pi 3 (✓ sudah ada)
- USB Microphone (~Rp 100k)
- Speaker 3.5mm (~Rp 150k)
- MicroSD Card 32GB (~Rp 150k)
- Kamera USB (~Rp 200k)

###### Software Implementation:
```python
# requirements.txt
speech_recognition==3.8.1
pyttsx3==2.90
opencv-python==4.5.1.48
numpy==1.19.5
requests==2.25.1
flask==2.0.1

###### Software Implementation dengan API Integration:
```python
import openai  # atau API provider lain
import speech_recognition as sr
import pyttsx3
import requests
import json
from datetime import datetime
import os

class ApiBaymax:
    def __init__(self):
        # Setup hardware interfaces
        self.recognizer = sr.Recognizer()
        self.microphone = sr.Microphone()
        self.tts_engine = pyttsx3.init()
        self.setup_voice()
        
        # API configuration
        self.api_key = os.getenv('OPENAI_API_KEY')  # atau API key lain
        self.conversation_history = []
        self.context_data = {}
        
        # Personality prompt
        self.system_prompt = """
        Anda adalah Baymax, personal healthcare companion yang caring dan helpful.
        Personality: Warm, gentle, health-focused, slightly formal tapi friendly.
        Selalu prioritaskan keselamatan dan kesehatan user.
        Respond dalam bahasa Indonesia dengan tone yang menenangkan.
        """
        
    def setup_voice(self):
        voices = self.tts_engine.getProperty('voices')
        self.tts_engine.setProperty('voice', voices[0].id)
        self.tts_engine.setProperty('rate', 140)  # Calm, measured speech
        self.tts_engine.setProperty('volume', 0.8)
        
    def listen(self):
        try:
            with self.microphone as source:
                print("🎤 Mendengarkan...")
                self.recognizer.adjust_for_ambient_noise(source, duration=0.5)
                audio = self.recognizer.listen(source, timeout=5, phrase_time_limit=10)
            
            # Convert speech to text
            text = self.recognizer.recognize_google(audio, language='id-ID')
            print(f"👤 User: {text}")
            return text
            
        except sr.WaitTimeoutError:
            return None
        except sr.UnknownValueError:
            self.speak("Maaf, saya tidak dapat memahami. Bisa diulangi?")
            return None
        except Exception as e:
            print(f"Error in speech recognition: {e}")
            return None
    
    def speak(self, text):
        print(f"🤖 Baymax: {text}")
        self.tts_engine.say(text)
        self.tts_engine.runAndWait()
    
    def collect_context_data(self):
        """Collect environmental and temporal context"""
        current_time = datetime.now()
        
        # Bisa ditambah sensor data nanti
        context = {
            'timestamp': current_time.isoformat(),
            'hour': current_time.hour,
            'day_of_week': current_time.strftime('%A'),
            'date': current_time.strftime('%Y-%m-%d'),
            # 'temperature': self.read_temperature(),  # uncomment when sensor ready
            # 'humidity': self.read_humidity(),
            # 'motion_detected': self.read_motion(),
        }
        
        return context
    
    def get_ai_response(self, user_input):
        """Get response dari AI API dengan context awareness"""
        try:
            # Collect current context
            context = self.collect_context_data()
            
            # Prepare context-enriched prompt
            context_info = f"""
            Current context:
            - Time: {context['hour']}:00, {context['day_of_week']}
            - Date: {context['date']}
            
            User input: {user_input}
            
            Please respond as Baymax dengan mempertimbangkan context waktu dan situation.
            """
            
            # API call (contoh dengan OpenAI format)
            response = requests.post(
                'https://api.openai.com/v1/chat/completions',
                headers={
                    'Authorization': f'Bearer {self.api_key}',
                    'Content-Type': 'application/json',
                },
                json={
                    'model': 'gpt-3.5-turbo',
                    'messages': [
                        {'role': 'system', 'content': self.system_prompt},
                        {'role': 'user', 'content': context_info}
                    ],
                    'max_tokens': 150,
                    'temperature': 0.7
                }
            )
            
            if response.status_code == 200:
                result = response.json()
                ai_response = result['choices'][0]['message']['content']
                
                # Store conversation history
                self.conversation_history.append({
                    'user': user_input,
                    'baymax': ai_response,
                    'timestamp': datetime.now().isoformat(),
                    'context': context
                })
                
                return ai_response.strip()
            else:
                return "Maaf, saya sedang mengalami masalah koneksi. Coba lagi sebentar?"
                
        except Exception as e:
            print(f"API Error: {e}")
            return self.get_fallback_response(user_input)
    
    def get_fallback_response(self, user_input):
        """Offline fallback responses"""
        current_hour = datetime.now().hour
        
        # Time-based greetings
        if current_hour < 10:
            greeting = "Selamat pagi"
        elif current_hour < 15:
            greeting = "Selamat siang"
        elif current_hour < 18:
            greeting = "Selamat sore"
        else:
            greeting = "Selamat malam"
        
        # Simple pattern matching untuk common queries
        user_lower = user_input.lower()
        
        if any(word in user_lower for word in ['sakit', 'pusing', 'demam']):
            return f"{greeting}. Saya khawatir dengan kondisi Anda. Apakah sudah minum air yang cukup? Jika keluhan berlanjut, sebaiknya konsultasi dengan dokter."
        
        elif any(word in user_lower for word in ['terima kasih', 'makasih']):
            return "Sama-sama! Saya senang bisa membantu. Kesehatan Anda adalah prioritas saya."
        
        elif any(word in user_lower for word in ['halo', 'hai', 'hello']):
            return f"{greeting}! Saya Baymax, personal healthcare companion Anda. Ada yang bisa saya bantu?"
        
        else:
            return f"{greeting}! Maaf, saya tidak dapat memberikan respon yang tepat saat ini. Coba tanyakan tentang kesehatan atau aktivitas harian Anda."

# Main execution
def main():
    print("🚀 Initializing Baymax...")
    baymax = ApiBaymax()
    
    # Initial greeting
    baymax.speak("Halo! Saya Baymax, personal healthcare companion Anda. Bagaimana perasaan Anda hari ini?")
    
    while True:
        try:
            user_input = baymax.listen()
            
            if user_input:
                if 'berhenti' in user_input.lower() or 'bye' in user_input.lower():
                    baymax.speak("Sampai jumpa! Jaga kesehatan dan istirahat yang cukup ya.")
                    break
                
                # Get AI response
                response = baymax.get_ai_response(user_input)
                baymax.speak(response)
                
        except KeyboardInterrupt:
            baymax.speak("Sampai jumpa! Tetap jaga kesehatan.")
            break
        except Exception as e:
            print(f"Main loop error: {e}")
            time.sleep(1)

if __name__ == "__main__":
    main()
```
```

###### Fitur Fase 1 dengan API Integration:
- ✓ Voice recognition (Bahasa Indonesia) 
- ✓ AI-powered conversation via API (OpenAI/Claude/Gemini)
- ✓ Context-aware responses dengan sensor data
- ✓ Personality Baymax yang consistent
- ✓ Conversation history tracking
- ✓ Offline fallback untuk basic queries
- ✓ Health-focused recommendations
- ✓ Time-based adaptive responses

##### FASE 2: Context Awareness (Minggu 5-8)
**Target**: Menambah pemahaman konteks dasar

###### Hardware Tambahan:
- DHT22 Temperature/Humidity sensor (~Rp 50k)
- PIR Motion sensor (~Rp 30k)
- Light sensor (LDR) (~Rp 20k)

###### Enhanced Features:
```python
class ContextAwareBaymax(BasicBaymax):
    def __init__(self):
        super().__init__()
        self.user_patterns = {}
        self.environmental_data = {}
        
    def collect_environmental_data(self):
        # Sensor readings
        temperature = self.read_temperature()
        humidity = self.read_humidity()
        light_level = self.read_light()
        motion_detected = self.read_motion()
        
        return {
            'temp': temperature,
            'humidity': humidity, 
            'light': light_level,
            'motion': motion_detected,
            'timestamp': datetime.datetime.now()
        }
    
    def analyze_context(self):
        env_data = self.collect_environmental_data()
        
        # Simple context rules
        if env_data['temp'] > 28:
            return "ac_suggestion"
        elif env_data['light'] < 30 and datetime.datetime.now().hour > 22:
            return "sleep_reminder"
        elif not env_data['motion'] and datetime.datetime.now().hour < 12:
            return "activity_encouragement"
            
        return "normal"
    
    def contextual_response(self, user_input):
        context = self.analyze_context()
        
        responses = {
            "ac_suggestion": "Cuaca cukup panas hari ini. Apakah Anda ingin saya mengingatkan untuk minum lebih banyak air?",
            "sleep_reminder": "Sepertinya sudah waktunya istirahat. Tidur yang cukup penting untuk kesehatan Anda.",
            "activity_encouragement": "Hari yang baik untuk sedikit bergerak! Bagaimana kalau kita lakukan stretching ringan?"
        }
        
        return responses.get(context, self.get_context_response(user_input))
```

##### FASE 3: Smart Integration (Minggu 9-12)
**Target**: Integrasi dengan smartphone dan cloud services

###### Implementasi:
- Companion app Android/iOS untuk processing advanced
- Cloud API untuk NLP yang lebih canggih
- Basic predictive capabilities
- Integration dengan Google Calendar/reminder apps

##### FASE 4: Learning & Personalization (Minggu 13-16)
**Target**: Adaptive behavior dan personal patterns

### 4. Implementasi Step-by-Step

##### Langkah 1: Setup Raspberry Pi 3 dengan API Integration
```bash
# Install Raspberry Pi OS
# Update system
sudo apt update && sudo apt upgrade -y

# Install Python dependencies
sudo apt install python3-pip python3-venv portaudio19-dev

# Create virtual environment
python3 -m venv baymax_env
source baymax_env/bin/activate

# Install required packages dengan API support
pip install speech_recognition pyttsx3 requests openai anthropic google-generativeai
pip install opencv-python numpy flask python-dotenv
```

##### Langkah 2: API Setup & Configuration
```bash
# Create environment file
nano .env

# Add your API keys:
OPENAI_API_KEY=your_openai_key_here
ANTHROPIC_API_KEY=your_claude_key_here  
GOOGLE_API_KEY=your_gemini_key_here

# Choose one or implement multiple providers for fallback
```

##### Langkah 2: Hardware Connections
```
GPIO Connections:
- DHT22: GPIO 4
- PIR Sensor: GPIO 18  
- Light Sensor: GPIO 24
- USB Mic: USB port
- Speaker: 3.5mm audio jack
- Camera: USB port
```

##### Langkah 3: Enhanced Code Structure dengan API
```python
# main.py
import time
import os
from dotenv import load_dotenv
from api_baymax import ApiBaymax

# Load environment variables
load_dotenv()

def main():
    print("🏥 Starting Baymax Healthcare Companion...")
    
    # Check API key
    if not os.getenv('OPENAI_API_KEY'):
        print("❌ Error: API key not found. Please set OPENAI_API_KEY in .env file")
        return
    
    # Initialize Baymax
    baymax = ApiBaymax()
    
    # Startup sequence
    baymax.speak("Sistem Baymax sedang online. Performing health check...")
    time.sleep(1)
    baymax.speak("Semua sistem normal. Halo! Saya Baymax, personal healthcare companion Anda.")
    
    # Main conversation loop
    conversation_count = 0
    while True:
        try:
            user_input = baymax.listen()
            
            if user_input:
                conversation_count += 1
                print(f"📊 Conversation #{conversation_count}")
                
                # Check for exit commands
                if any(word in user_input.lower() for word in ['berhenti', 'selesai', 'bye', 'keluar']):
                    baymax.speak("Terima kasih telah menggunakan Baymax. Jaga kesehatan dan sampai jumpa!")
                    break
                
                # Process with AI
                response = baymax.get_ai_response(user_input)
                baymax.speak(response)
                
                # Periodic health reminder
                if conversation_count % 10 == 0:
                    baymax.speak("Reminder: Jangan lupa minum air dan istirahat sejenak untuk mata Anda.")
                
        except KeyboardInterrupt:
            baymax.speak("Sistem Baymax shutting down. Sampai jumpa dan tetap sehat!")
            break
        except Exception as e:
            print(f"🚨 System error: {e}")
            baymax.speak("Maaf, ada gangguan kecil. Mari kita coba lagi.")
            time.sleep(2)

if __name__ == "__main__":
    main()
```

##### Langkah 4: Multiple API Provider Support
```python
# api_providers.py - Support multiple AI providers
import openai
import anthropic
import google.generativeai as genai

class APIProvider:
    def __init__(self):
        self.providers = {
            'openai': self.openai_call,
            'claude': self.claude_call,
            'gemini': self.gemini_call
        }
        self.current_provider = 'openai'  # default
    
    def openai_call(self, messages):
        response = openai.ChatCompletion.create(
            model="gpt-3.5-turbo",
            messages=messages,
            max_tokens=150,
            temperature=0.7
        )
        return response.choices[0].message.content
    
    def claude_call(self, messages):
        client = anthropic.Anthropic()
        response = client.messages.create(
            model="claude-3-sonnet-20240229",
            max_tokens=150,
            messages=messages
        )
        return response.content[0].text
    
    def gemini_call(self, messages):
        model = genai.GenerativeModel('gemini-pro')
        prompt = messages[-1]['content']  # Simple implementation
        response = model.generate_content(prompt)
        return response.text
    
    def get_response(self, messages):
        try:
            return self.providers[self.current_provider](messages)
        except Exception as e:
            print(f"Error with {self.current_provider}: {e}")
            # Try fallback providers
            for provider in ['openai', 'claude', 'gemini']:
                if provider != self.current_provider:
                    try:
                        return self.providers[provider](messages)
                    except:
                        continue
            return None
```

### 5. Budget Estimasi

##### Hardware Minimum (Fase 1):
- USB Microphone: Rp 100.000
- Speaker: Rp 150.000
- MicroSD 32GB: Rp 150.000
- USB Camera: Rp 200.000
- **Total Fase 1: ~Rp 600.000**

##### Hardware Lengkap (Semua Fase):
- Sensors (DHT22, PIR, LDR): Rp 100.000
- Better Camera (HD): Rp 300.000
- Better Speaker: Rp 200.000
- Case & Mounting: Rp 150.000
- **Total Keseluruhan: ~Rp 1.350.000**

### 6. Optimasi untuk RPi 3

##### Performance Tips:
```python
# Gunakan threading untuk sensor reading
import threading

def sensor_thread():
    while True:
        collect_sensor_data()
        time.sleep(30)  # Update every 30 seconds

# Optimize speech recognition
recognizer.energy_threshold = 4000  # Adjust for environment
recognizer.pause_threshold = 0.5    # Faster response
```

##### Memory Management:
- Gunakan swap file untuk extra memory
- Optimize model loading (load once, reuse)
- Implement cleanup routines

### 7. Testing & Debugging

##### Test Cases:
1. Voice recognition accuracy dalam bahasa Indonesia
2. Response time untuk various queries
3. Context detection accuracy
4. System stability untuk long-running sessions
5. Emergency scenario handling

##### Monitoring:
```python
# Add logging untuk monitoring
import logging

logging.basicConfig(
    filename='baymax.log',
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)
```

### Kesimpulan

Dengan Raspberry Pi 3, Kita bisa mulai dengan Baymax yang functional meski tidak se-advanced konsep original. 


Bagian pentingnya :
1. **Start Simple**: Mulai dengan voice assistant basic
2. **Iterate Fast**: Tambah fitur secara bertahap
3. **Leverage Cloud**: Gunakan cloud untuk heavy processing
4. **Focus on UX**: Prioritas pada experience yang smooth
