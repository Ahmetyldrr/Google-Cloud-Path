# Module 4: Kimlik Doğrulama ve Yetkilendirme

Bu modülde, Google Cloud üzerinde uygulama kullanıcıları ve servisleri için kimlik doğrulama (authentication) ve yetkilendirme (authorization) süreçlerini öğreneceksiniz. Aşağıda IAM, servis hesapları, API anahtarları ve diğer kimlik yöntemleri hakkında detaylı bilgiler ve örnek komutlar yer alıyor.

---

## 1. IAM (Identity and Access Management)

### 1.1. Temel Kavramlar

- **Principal (Özne):** Kimliğin tanımlandığı varlık; Google Account, Service Account, Google Group, Workspace Domain veya Cloud Identity Domain olabilir.  
- **Resource (Kaynak):** Erişim denetimi uygulanan varlık; proje, VM instance, bucket, vb.  
- **Permission (İzin):** Kaynak üzerinde gerçekleştirilebilecek işlem, örn. `compute.instances.start`.  
- **Role (Rol):** İzinlerin koleksiyonu; temel (Basic), ön tanımlı (Predefined) veya özel (Custom) olabilir.

### 1.2. Roller ve İzinler

- **Basic Roles:** `roles/viewer`, `roles/editor`, `roles/owner`  
- **Predefined Roles:** Google tarafından tanımlı, örn. `roles/run.invoker`  
- **Custom Roles:** Kendi ihtiyacınıza göre oluşturduğunuz roller  

```bash
# Örnek: 'staff' grubuna Compute Admin rolü atama
gcloud projects add-iam-policy-binding my-project   --member=group:staff@example.com   --role=roles/compute.instanceAdmin
```

---

## 2. Servis Hesapları (Service Accounts)

### 2.1. Nedir ve Ne İçin Kullanılır?

- Uygulamalar veya compute iş yükleri için kimlik sunar.  
- Browser tabanlı oturum açma yerine RSA anahtar çiftleri kullanır.  
- Parola yerine JSON formatında özel anahtar dosyası (`key.json`) ile kimlik doğrulama yapılır.

### 2.2. Servis Hesabı Oluşturma ve Rol Atama

```bash
# Servis hesabı oluştur
gcloud iam service-accounts create my-service-account   --display-name="My App SA"

# Servis hesabına rol ata
gcloud projects add-iam-policy-binding my-project   --member="serviceAccount:my-service-account@my-project.iam.gserviceaccount.com"   --role="roles/storage.objectAdmin"
```

### 2.3. Anahtar İndirme ve Kullanım Riski

```bash
# Servis hesabı anahtarı indir
gcloud iam service-accounts keys create key.json   --iam-account=my-service-account@my-project.iam.gserviceaccount.com

# Uygulamada ADC kullanımı
export GOOGLE_APPLICATION_CREDENTIALS="$(pwd)/key.json"
```

> **Uyarı:** Anahtar dosyasının Git gibi depolara eklenmesi `credential leakage` riskine yol açar.

---

## 3. API Anahtarları ve OAuth

### 3.1. API Anahtarları (API Keys)

- Basit, düşük güvenlikli read-only API'ler için uygundur.  
- Herhangi bir kullanıcı anahtarı ele geçirirse, faturalandırma ve kota projenize gider.

```bash
# API anahtarı oluşturma
gcloud services api-keys create --display-name="My API Key"

# Anahtarı kullanarak örnek çağrı
curl "https://maps.googleapis.com/maps/api/geocode/json?address=Istanbul&key=API_KEY"
```

### 3.2. OAuth 2.0

- Kullanıcı kimliği ile erişim sağlar.  
- Token sürelidir, yenilenebilir.

```bash
# OAuth token alma (gcloud ile)
gcloud auth application-default print-access-token
```

---

## 4. Diğer Kimlik Metotları

- **OAuth User Account:** Gerçek kullanıcılar için; e-posta ve parola ile alınan token.  
- **Identity-Aware Proxy (IAP):** Web uygulamaları için katmanlı erişim kontrolü.  
- **Identity Platform (Firebase SDK):** Federated kimlik yönetimi (Google, Facebook, SAML).

```javascript
// Firebase SDK ile Google oturumu açma (örnek)
import firebase from "firebase/app";
import "firebase/auth";

firebase.initializeApp({ /* config */ });

firebase.auth().signInWithPopup(new firebase.auth.GoogleAuthProvider())
  .then(user => console.log("Kullanıcı:", user))
  .catch(err => console.error(err));
```

---

## 5. Secret Manager

- Şifre, API anahtarı, sertifika gibi gizli verileri güvenle depolayın.  
- Versiyonlama, erişim denetimi ve otomatik şifreleme sağlar.

```bash
# Secret oluşturma
echo -n "my-secret-value" | gcloud secrets create my-secret --data-file=-

# Yeni versiyon ekleme
echo -n "updated-value" | gcloud secrets versions add my-secret --data-file=-

# Secret okuma
gcloud secrets versions access latest --secret=my-secret
```

---

## Özet ve En İyi Uygulamalar

1. **Least Privilege:** Roller en az yetki prensibine göre tanımlansın.  
2. **Anahtar Yönetimi:** Servis hesabı anahtarlarını minimal kullanın; ADC ve Workload Identity tercih edin.  
3. **Gizli Bilgi Yönetimi:** Şifre ve anahtarları Secret Manager’da tutun.  
4. **Çok Faktörlü Kimlik:** Mümkünse MFA ve IAP kullanarak ek güvenlik katmanları ekleyin.
