# BabyNest App - Architecture & Workflow

## 📌 Overview
This document outlines the **architecture, repository structure, and workflow** of a pregnancy tracking app built with **React Native** for frontend and on-device AI processing.

### **Key Features**
- **Pregnancy Tracking**: Weekly updates on baby growth, milestones
- **Appointments & Reminders**: Local storage & push notifications
- **Offline Chatbot**: Llama.cpp-based AI assistant for pregnancy queries
- **On-Device Processing**: No cloud dependency, everything runs locally
- **Vector Database for FAQs**: Efficient retrieval of pregnancy-related information

---

## **🛠️ Architecture**

```
Frontend (React Native)
├── UI Components (Pregnancy Tracker, Calendar, Chatbot)
├── Local Storage (SQLite/MMKV for user data)
├── AI Processing (Llama.cpp for offline chatbot)
├── Vector Database (Qdrant/FAISS for pregnancy FAQs)
└── Notification System (Push notifications for reminders)
```

### **Data Flow**

```
[User Input]
      ↓
[React Native UI]
      ↓
[Local AI (Chatbot + NLP)]
      ↓
[Vector Database (Pregnancy FAQs)]
      ↓
[SQLite/MMKV (User Data + Appointments)]
      ↓
[Notification System (Reminders)]
```

---

## **📂 Repository Structure**
```
pregnancy-tracker-app/
├── src/
│   ├── components/        # UI components (calendar, chatbot UI)
│   ├── screens/           # Screens (Home, Chat, Appointments)
│   ├── storage/           # Local Storage (SQLite/MMKV)
│   ├── ai/                # AI processing
│   │   ├── chatbot/       # LLM-based chatbot
│   │   ├── vector_db/     # Vector storage (Qdrant/FAISS)
│   │   ├── nlp/           # NLP tokenization, embeddings
│   ├── utils/             # Helper functions
│   ├── notifications/     # Appointment reminders
├── android/               # Android config
├── ios/                   # iOS config
├── package.json           # Dependencies
└── App.js                 # Main entry file
```

---

## **📊 Workflow Diagram**

```
    A[User Input] -->|Enters Data| B[React Native UI]
    B -->|Processes Locally| C[Local AI (Llama.cpp)]
    C -->|Retrieves Similar Queries| D[Vector Database (Qdrant)]
    D -->|Returns Relevant Info| C
    C -->|Generates Response| B
    B -->|Stores Data| E[SQLite/MMKV]
    B -->|Schedules Notifications| F[Push Notifications]
    E -->|Fetches Data| B
    F -->|Sends Reminder| A
```

---

## **🔹 Feature Breakdown**

### **1️⃣ Pregnancy Tracking**
- Track weeks, milestones, baby growth
- Data stored locally using SQLite/MMKV

```sql
CREATE TABLE pregnancy_data (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  user_id TEXT,
  week INTEGER,
  milestone TEXT,
  tips TEXT
);
```

---

### **2️⃣ Appointment & Reminders**
- Local storage of doctor appointments
- Push notifications for reminders

```javascript
import PushNotification from 'react-native-push-notification';

export const scheduleReminder = (title, message, date) => {
  PushNotification.localNotificationSchedule({
    title,
    message,
    date,
    allowWhileIdle: true,
  });
};
```

---

### **3️⃣ Chatbot (Offline AI)**
- Uses **llama.cpp** for AI processing
- Runs locally without internet

```javascript
import { exec } from 'react-native-llama-cpp';

export const runPregnancyBot = async (question) => {
  const response = await exec(`You are a pregnancy assistant. Answer: ${question}`);
  return response;
};
```

---

### **4️⃣ Vector Database for FAQs**
- Stores embeddings of pregnancy FAQs
- Retrieves similar questions before calling LLM

```javascript
import { QdrantClient } from 'qdrant-client';

const qdrant = new QdrantClient({ url: 'http://localhost:6333' });

export const searchPregnancyFAQ = async (queryVector) => {
  return await qdrant.search({
    collection_name: 'pregnancy_faqs',
    vector: queryVector,
    limit: 1
  });
};
```

---

