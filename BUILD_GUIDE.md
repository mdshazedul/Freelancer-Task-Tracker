# 📱 কাজ ট্র্যাকার — Android APK Build Guide

## ✅ ফিচার সমূহ

| ফিচার | বিবরণ |
|-------|--------|
| **কাজ ট্র্যাকিং** | তৈরি, সম্পাদনা, মুছুন — ক্যাটাগরি ও তারিখ সহ |
| **PIN নিরাপত্তা** | ৪-সংখ্যার PIN, ৩ বার ভুলে লক |
| **OAuth লগইন** | Google/GitHub দিয়ে লগইন |
| **📊 পরিসংখ্যান** | চার্ট, মাসিক অগ্রগতি, ক্যাটাগরি বিশ্লেষণ |
| **✨ সহায় AI** | AI চ্যাটবট — ফ্রিল্যান্সিং পরামর্শ |
| **📵 অফলাইন মোড** | ইন্টারনেট ছাড়া কাজ করা যাবে, অনলাইনে ফিরলে স্বয়ংক্রিয় সিঙ্ক |

---

## 📵 অফলাইন সাপোর্ট — কীভাবে কাজ করে

```
অফলাইন হলে:
  ✓ আগে দেখা tasks → ক্যাশ (AsyncStorage) থেকে দেখায়
  ✓ নতুন কাজ যোগ/আপডেট → "পেন্ডিং সিঙ্ক" হিসেবে সংরক্ষিত
  ✓ মুছে ফেলা → অফলাইনে optimistically সরে যায়
  ✓ লাল ব্যানার দেখায়: "📵 অফলাইন — X টি পরিবর্তন পেন্ডিং"

অনলাইনে ফিরলে:
  ✓ স্বয়ংক্রিয়ভাবে সব পেন্ডিং পরিবর্তন সার্ভারে সিঙ্ক হয়
  ✓ নীল ব্যানার: "সার্ভারে সিঙ্ক হচ্ছে..."
  ✓ সফল হলে ব্যানার অদৃশ্য
```

---

## 📁 প্রজেক্ট ফাইল কাঠামো

```
mobile/
├── src/
│   ├── App.tsx                    ← মূল এন্ট্রি (URL এখানে বদলাতে হবে)
│   ├── screens/
│   │   ├── DashboardScreen.tsx    ← কাজের তালিকা (অফলাইন সক্ষম)
│   │   ├── StatsScreen.tsx        ← চার্ট ও পরিসংখ্যান (অফলাইন সক্ষম)
│   │   ├── AIChatScreen.tsx       ← সহায় AI চ্যাটবট
│   │   ├── TaskFormScreen.tsx     ← কাজ যোগ/সম্পাদনা (অফলাইন সক্ষম)
│   │   ├── HomeScreen.tsx         ← ল্যান্ডিং পেজ
│   │   ├── LoginScreen.tsx        ← OAuth লগইন
│   │   ├── PinSetupScreen.tsx     ← পিন তৈরি
│   │   ├── PinVerifyScreen.tsx    ← পিন যাচাই
│   │   └── SettingsScreen.tsx     ← সেটিংস
│   ├── components/
│   │   ├── OfflineBanner.tsx      ← অফলাইন ব্যানার ← নতুন
│   │   ├── PinPad.tsx             ← নম্বর প্যাড UI
│   │   └── TaskCard.tsx           ← কাজের কার্ড (পেন্ডিং ইন্ডিকেটর সহ)
│   ├── hooks/
│   │   ├── useOfflineTasks.ts     ← অফলাইন-সক্ষম tasks hook ← নতুন
│   │   ├── useOfflineCache.ts     ← AsyncStorage ক্যাশ ম্যানেজার ← নতুন
│   │   ├── useNetworkStatus.ts    ← নেটওয়ার্ক detection ← নতুন
│   │   ├── useDevicePin.ts        ← পিন ম্যানেজমেন্ট
│   │   └── useAuth.ts             ← অথেনটিকেশন
│   ├── api/
│   │   ├── client.ts              ← tRPC ক্লায়েন্ট
│   │   └── router-type.ts         ← API টাইপ সংজ্ঞা
│   └── types/index.ts
├── app.json                       ← Expo কনফিগ
├── eas.json                       ← EAS Build কনফিগ
└── package.json                   ← Dependencies
```

---

## ধাপ ১ — ওয়েব অ্যাপ Deploy করুন

Replit-এ **"Deploy"** বাটনে ক্লিক করুন।
URL পাবেন: `https://আপনার-অ্যাপ.replit.app`

---

## ধাপ ২ — URL আপডেট করুন

`mobile/src/App.tsx` লাইন ২৪:
```typescript
export const BASE_URL = "https://আপনার-আসল-url.replit.app";
```

---

## ধাপ ৩ — PC-তে Setup করুন

```bash
# Node.js 18+ লাগবে: https://nodejs.org
npm install -g eas-cli
eas login   # expo.dev-এ বিনামূল্যে অ্যাকাউন্ট খুলুন
```

---

## ধাপ ৪ — Dependencies ইনস্টল করুন

```bash
cd mobile
npm install
```

> **নতুন packages যোগ হয়েছে:**
> - `@react-native-async-storage/async-storage` — অফলাইন ক্যাশ
> - `@react-native-community/netinfo` — নেটওয়ার্ক detection

---

## ধাপ ৫ — EAS Initialize করুন

```bash
eas build:configure
```

---

## ধাপ ৬ — APK Build করুন

```bash
# APK (মোবাইলে সরাসরি ইনস্টলযোগ্য)
eas build --platform android --profile preview
```

⏱ **সময়**: ১০-২০ মিনিট | ☁️ ক্লাউডে হয় — Android Studio লাগে না

---

## ধাপ ৭ — ইনস্টল করুন

1. APK ডাউনলোড করুন
2. Android: **Settings → Security → Unknown Sources** চালু করুন
3. APK ইনস্টল করুন ✅

---

## 🧪 Expo Go দিয়ে লোকাল টেস্ট

```bash
npx expo start   # QR স্ক্যান করুন → Expo Go অ্যাপে দেখুন
```

---

## ট্যাব বার

| ট্যাব | কাজ |
|-------|-----|
| 📋 কাজ | কাজ তালিকা, যোগ, সম্পাদনা, মুছুন — অফলাইনে কাজ করে |
| 📊 স্ট্যাটস | চার্ট, মাসিক অগ্রগতি — ক্যাশ থেকে দেখায় |
| ✨ সহায় AI | AI চ্যাটবট (অনলাইন প্রয়োজন) |
| ⚙️ সেটিংস | প্রোফাইল, পিন রিসেট, লগআউট |

---

## সমস্যা হলে

```bash
npx expo start --clear        # Cache পরিষ্কার
rm -rf node_modules && npm install   # Dependencies পুনরায় ইনস্টল
```
