# 💕  Matrimony Match Analyser

> A modern, intelligent matrimony matchmaking platform built on Databricks with traditional Tamil star compatibility (Thirumana Porutham)

[![Databricks](https://img.shields.io/badge/Databricks-FF3621?style=for-the-badge&logo=databricks&logoColor=white)](https://databricks.com/)
[![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![Gradio](https://img.shields.io/badge/Gradio-FF7C00?style=for-the-badge&logo=gradio&logoColor=white)](https://gradio.app/)
[![Delta Lake](https://img.shields.io/badge/Delta_Lake-00ADD8?style=for-the-badge)](https://delta.io/)

## 📖 Overview

 Matrimony Match Analyser is a full-stack matrimony matchmaking application that combines modern data engineering practices with traditional Tamil astrological compatibility matching. Built entirely on Databricks, it provides a secure, scalable platform for finding compatible life partners based on birth stars (Nakshatras) and other preferences.

### ✨ Key Features

#### 🔐 **Secure Authentication**
- Email-based user registration with OTP verification
- SMTP integration for automated email delivery
- Password hashing with PBKDF2-HMAC-SHA256
- Complete audit trail of all authentication events
- Session management with secure login/logout

#### 🎯 **Intelligent Matchmaking**
- **Star Compatibility (Thirumana Porutham)**: Traditional Tamil astrological matching
  - Utthamam (Best Match) - 🥇
  - Madhyamam (Good Match) - 🥈
- **Advanced Filtering**:
  - Birth year range
  - Gender (shows opposite gender profiles)
  - Sub-caste/community
  - Preferred stars (Nakshatras)
  - Preferred Rasi (moon signs)
  - Profile exclusion list

#### 📊 **Rich Profile Information**
- 6,892+ active profiles (Female: 1,907 | Male: 4,978)
- Detailed personal information
- Education and career details
- Contact information
- Profile photos stored in Unity Catalog Volumes
- Tamil translations for stars and rasis

#### 📈 **Analytics & Visualizations**
- Star distribution charts
- Rasi (zodiac) distribution
- Birth year trends
- Gender demographics
- Interactive Plotly charts

#### ⚡ **Performance Optimized**
- In-memory data caching for instant filtering
- Background data loading on app startup
- Image caching for fast profile viewing
- SQL query retry logic with exponential backoff
- Handles 6,000+ profiles with sub-second response times

---

## 🏗️ Architecture

### Data Pipeline (Bronze → Gold)

```
┌─────────────────┐
│  Bronze Layer   │  Raw scraped matrimony profiles
│  (Raw Data)     │  → matrimony.default.bronze_profiles
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   Gold Layer    │  Cleaned, parsed, enriched profiles
│  (Curated)      │  → matrimony.default.gold_profiles
│                 │  + Star compatibility enrichment
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Compatibility   │  Traditional Tamil star matching rules
│     Table       │  → matrimony.default.star_compatibility
└─────────────────┘  (458 compatibility rules)
```

### Application Architecture

```
┌──────────────────────────────────────────────────────┐
│                  Databricks App                      │
│              (Gradio UI + Python)                    │
├──────────────────────────────────────────────────────┤
│  Authentication Layer                                │
│  ├─ Email/Password Login                            │
│  ├─ OTP Email Verification (Gmail SMTP)             │
│  └─ Audit Logging                                   │
├──────────────────────────────────────────────────────┤
│  Data Layer (In-Memory Cache)                       │
│  ├─ Gold Profiles (6,892 records)                   │
│  ├─ Compatibility Rules (458 records)               │
│  └─ Profile Images (UC Volume)                      │
├──────────────────────────────────────────────────────┤
│  Business Logic                                      │
│  ├─ Filtering Engine                                │
│  ├─ Compatibility Matcher                           │
│  ├─ Profile Viewer                                  │
│  └─ Analytics Generator                             │
└──────────────────────────────────────────────────────┘
         │
         ▼
┌──────────────────────────────────────────────────────┐
│           Unity Catalog (Delta Tables)               │
│  ├─ matrimony.default.gold_profiles                  │
│  ├─ matrimony.default.star_compatibility             │
│  ├─ matrimony.default.app_users                      │
│  ├─ matrimony.default.app_otp_codes                  │
│  ├─ matrimony.default.app_audit_logs                 │
│  └─ Volume: matrimony.default.profile_images         │
└──────────────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

### **Data Platform**
- **Databricks** - Unified analytics platform
- **Delta Lake** - ACID transactions, time travel, schema evolution
- **Unity Catalog** - Unified governance, data lineage, access control
- **SQL Warehouse** - Serverless compute for SQL queries

### **Application**
- **Python 3.9+** - Core application language
- **Gradio 4.x** - Web UI framework
- **Pandas** - Data manipulation
- **Plotly** - Interactive visualizations

### **Security & Auth**
- **OAuth 2.0** - Databricks App service principal authentication
- **PBKDF2-HMAC-SHA256** - Password hashing
- **SMTP (Gmail)** - Email delivery for OTP

### **Infrastructure**
- **Databricks Apps** - Serverless app hosting
- **UC Volumes** - File storage for profile images
- **Databricks SDK** - Python client for Databricks APIs

---

## 🚀 Setup & Installation

### Prerequisites

1. **Databricks Workspace** (AWS/Azure/GCP)
2. **Unity Catalog enabled**
3. **SQL Warehouse** (any size)
4. **Gmail Account** with App Password (for OTP emails)

### Step 1: Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/Match-Analyser.git
cd -Match-Analyser
```

### Step 2: Upload to Databricks Workspace

```bash
# Using Databricks CLI
databricks workspace import-dir . /Users/YOUR_EMAIL/match_finder_analyser --overwrite
```

Or manually upload via Databricks UI:
1. Go to Workspace → Users → Your Email
2. Create folder: `match_finder_analyser`
3. Upload `app.py`, `auth.py`, `app.yaml`

### Step 3: Prepare Data Tables

Run these SQL commands in Databricks SQL Editor:

```sql
-- Create catalog and schema
CREATE CATALOG IF NOT EXISTS matrimony;
CREATE SCHEMA IF NOT EXISTS matrimony.default;

-- Create Gold profiles table
CREATE TABLE matrimony.default.gold_profiles (
  reg_no STRING NOT NULL,
  name STRING,
  dob STRING,
  birth_year INT,
  birth_place STRING,
  star STRING,
  rasi STRING,
  caste STRING,
  sub_caste STRING,
  marital_status STRING,
  diet STRING,
  qualification STRING,
  job STRING,
  income_month_height_complexion STRING,
  own_house_nativity STRING,
  contact_person STRING,
  contact_no STRING,
  email_id STRING,
  best_match_stars STRING,
  good_match_stars STRING,
  gender STRING,
  image_volume_path STRING,
  CONSTRAINT gold_profiles_pk PRIMARY KEY (reg_no)
);

-- Create star compatibility table
CREATE TABLE matrimony.default.star_compatibility (
  boy_star STRING NOT NULL,
  girl_star STRING NOT NULL,
  match_level STRING NOT NULL,
  CONSTRAINT star_compat_pk PRIMARY KEY (boy_star, girl_star)
);

-- Create authentication tables
CREATE TABLE matrimony.default.app_users (
  email STRING NOT NULL,
  name STRING NOT NULL,
  password_hash STRING NOT NULL,
  password_salt STRING NOT NULL,
  status STRING NOT NULL,
  created_at TIMESTAMP NOT NULL,
  updated_at TIMESTAMP NOT NULL,
  CONSTRAINT app_users_pk PRIMARY KEY (email)
);

CREATE TABLE matrimony.default.app_otp_codes (
  email STRING NOT NULL,
  otp_code STRING NOT NULL,
  expires_at TIMESTAMP NOT NULL,
  used BOOLEAN NOT NULL DEFAULT false,
  created_at TIMESTAMP NOT NULL
);

CREATE TABLE matrimony.default.app_audit_logs (
  email STRING,
  event_type STRING NOT NULL,
  event_details STRING,
  created_at TIMESTAMP NOT NULL
);
```

### Step 4: Create UC Volume for Images

```sql
CREATE VOLUME matrimony.default.profile_images;
```

Upload profile images to:
- `/Volumes/matrimony/default/profile_images/female/{reg_no}.jpg`
- `/Volumes/matrimony/default/profile_images/male/{reg_no}.jpg`

### Step 5: Configure SMTP (Gmail)

1. **Enable 2-Step Verification** on your Gmail account:
   - Go to https://myaccount.google.com/security
   - Turn on "2-Step Verification"

2. **Generate App Password**:
   - Go to https://myaccount.google.com/apppasswords
   - Create app password for "Databricks Matrimony"
   - Copy the 16-character password

3. **Update `app.yaml`**:

```yaml
command:
  - python
  - app.py

env:
  - name: DATABRICKS_WAREHOUSE_ID
    valueFrom: sql-warehouse
  - name: SMTP_EMAIL
    value: "your-email@gmail.com"
  - name: SMTP_PASSWORD
    value: "your-16-char-app-password"
```

### Step 6: Create Databricks App

```bash
# Navigate to your project directory
cd /Workspace/Users/YOUR_EMAIL/match_finder_analyser

# Create the app
databricks apps create matchfinderanalyser \
  --source-code-path /Workspace/Users/YOUR_EMAIL/match_finder_analyser

# Add SQL Warehouse resource
databricks apps update matchfinderanalyser \
  --add-resource sql-warehouse:YOUR_WAREHOUSE_ID

# Start the app
databricks apps start matchfinderanalyser
```

### Step 7: Grant Permissions

Get your app's service principal ID:

```bash
databricks apps get matchfinderanalyser | grep service_principal_client_id
```

Grant table and volume permissions:

```sql
-- Replace APP_ID with your service_principal_client_id
GRANT SELECT ON TABLE matrimony.default.gold_profiles TO `APP_ID`;
GRANT SELECT ON TABLE matrimony.default.star_compatibility TO `APP_ID`;
GRANT SELECT, MODIFY ON TABLE matrimony.default.app_users TO `APP_ID`;
GRANT SELECT, MODIFY ON TABLE matrimony.default.app_otp_codes TO `APP_ID`;
GRANT SELECT, MODIFY ON TABLE matrimony.default.app_audit_logs TO `APP_ID`;
GRANT READ_VOLUME ON VOLUME matrimony.default.profile_images TO `APP_ID`;
```

### Step 8: Access Your App

Get your app URL:

```bash
databricks apps get matchfinderanalyser | grep url
```

Open the URL in your browser and sign up!

---

## 📱 Usage

### For New Users

1. **Sign Up**
   - Click "Sign Up" tab
   - Enter your name, email, and password (min 6 characters)
   - Click "Sign Up & Send OTP"
   - Check your email inbox (and spam folder) for the 6-digit OTP

2. **Verify OTP**
   - Go to "Verify OTP" tab
   - Enter your email and the 6-digit code
   - Click "Verify OTP"

3. **Login**
   - Go to "Login" tab
   - Enter your email and password
   - Click "Login"

### Main Features

#### **🔍 Browse Profiles**
1. Set your filters:
   - Gender (Male/Female)
   - Birth year range
   - Sub-caste
   - Preferred stars
   - Preferred rasi
   - Exclude specific profile IDs
2. Click "Apply Filters"
3. View results table with matched profiles

#### **👤 View Full Profile**
1. Note the `reg_no` from the table (e.g., F13073)
2. Enter it in "Reg No" field
3. Click "View Profile"
4. See complete details + profile photo

#### **⭐ Find Compatible Matches**
1. Go to "Compatibility" tab
2. Select your gender
3. Select your birth star
4. Click "Find Matches"
5. View Utthamam (Best) and Madhyamam (Good) matches

#### **📊 View Analytics**
1. Go to "Charts" tab
2. Click "Generate Charts"
3. View distribution charts for:
   - Stars
   - Rasi
   - Birth years
   - Gender

---

## 📂 Project Structure

```
match_finder_analyser/
├── app.py                  # Main Gradio application
├── auth.py                 # Authentication module
├── app.yaml                # Databricks App configuration
├── README.md               # This file
└── requirements.txt        # Python dependencies (optional)
```

### Key Files

#### **`app.py`** (829 lines)
- Gradio UI definition
- In-memory data caching
- Filtering and matching logic
- Profile viewer
- Chart generation
- Session management

#### **`auth.py`** (175 lines)
- User signup with validation
- OTP generation and email delivery
- Email verification
- Login authentication
- Audit logging

#### **`app.yaml`**
- App configuration
- Environment variables
- SQL Warehouse resource mapping

---

## 🗄️ Database Schema

### **gold_profiles** (6,892 rows)
```sql
reg_no                         STRING  PRIMARY KEY
name                           STRING
dob                            STRING
birth_year                     INT
birth_place                    STRING
star                           STRING
rasi                           STRING
caste                          STRING
sub_caste                      STRING
marital_status                 STRING
diet                           STRING
qualification                  STRING
job                            STRING
income_month_height_complexion STRING
own_house_nativity             STRING
contact_person                 STRING
contact_no                     STRING
email_id                       STRING
best_match_stars               STRING  -- Comma-separated utthamam stars
good_match_stars               STRING  -- Comma-separated madhyamam stars
gender                         STRING  -- Female / Male
image_volume_path              STRING  -- UC Volume path
```

### **star_compatibility** (458 rows)
```sql
boy_star      STRING  -- Boy's birth star
girl_star     STRING  -- Girl's birth star
match_level   STRING  -- UTTHAMAM / MADHYAMAM
PRIMARY KEY (boy_star, girl_star)
```

### **app_users**
```sql
email          STRING  PRIMARY KEY
name           STRING
password_hash  STRING  -- PBKDF2-HMAC-SHA256
password_salt  STRING
status         STRING  -- PENDING / ACTIVE
created_at     TIMESTAMP
updated_at     TIMESTAMP
```

### **app_otp_codes**
```sql
email       STRING
otp_code    STRING     -- 6-digit code
expires_at  TIMESTAMP  -- 10-minute expiry
used        BOOLEAN
created_at  TIMESTAMP
```

### **app_audit_logs**
```sql
email          STRING
event_type     STRING  -- SIGNUP, LOGIN_SUCCESS, LOGOUT, etc.
event_details  STRING
created_at     TIMESTAMP
```

---

## 🎨 UI Theme

The app uses a **romantic pink and rose theme** perfect for matrimony:
- **Primary**: Pink gradient (#ec407a → #f06292)
- **Background**: Soft pink gradient (#ffeef8 → #fff0f5)
- **Headings**: Deep rose (#c2185b)
- **Buttons**: Pink gradient with hover effects

---

## 🔒 Security Features

✅ **Password Security**
- PBKDF2-HMAC-SHA256 with 100,000 iterations
- Unique salt per user
- Secure comparison with `secrets.compare_digest()`

✅ **Email Verification**
- 6-digit OTP codes
- 10-minute expiry window
- One-time use enforcement
- SMTP TLS encryption

✅ **Session Management**
- Gradio State for logged-in user
- Automatic logout on session end
- Audit trail of all logins

✅ **Databricks Security**
- OAuth 2.0 service principal
- Unity Catalog RBAC
- Least-privilege access model

---

## 📊 Performance

- **Startup time**: ~5-10 seconds (background data load)
- **Filter response**: <100ms for 6,000+ profiles
- **Profile view**: <200ms (with image caching)
- **Compatibility matching**: <50ms
- **Concurrent users**: Supports 100+ simultaneous users

---

## 🐛 Troubleshooting

### OTP Emails Not Received

**Cause**: SMTP not configured or Gmail App Password incorrect

**Solution**:
1. Check `app.yaml` has correct `SMTP_EMAIL` and `SMTP_PASSWORD`
2. Verify 2-Step Verification is enabled on Gmail
3. Generate fresh App Password
4. Restart app: `databricks apps restart matchfinderanalyser`
5. Check spam/junk folder

### App Shows "Data Loading" Forever

**Cause**: SQL Warehouse not accessible or tables empty

**Solution**:
1. Check warehouse is running: `databricks sql-warehouses get YOUR_WAREHOUSE_ID`
2. Verify tables have data: `SELECT COUNT(*) FROM matrimony.default.gold_profiles`
3. Check app logs for SQL errors

### Profile Images Not Loading

**Cause**: Missing READ_VOLUME permission

**Solution**:
```sql
GRANT READ_VOLUME ON VOLUME matrimony.default.profile_images 
TO `YOUR_APP_SERVICE_PRINCIPAL_ID`;
```

### "Error" on OTP Verification

**Cause**: Auth tables missing or permission denied

**Solution**:
1. Check tables exist: `SHOW TABLES IN matrimony.default LIKE 'app_%'`
2. Grant permissions to app service principal (see Step 7)
3. Restart app

---

## 🚧 Future Enhancements

- [ ] **Advanced Search**: Full-text search across profiles
- [ ] **Favorites**: Save and bookmark preferred profiles
- [ ] **Messaging**: In-app chat between matched users
- [ ] **Mobile App**: React Native app with same backend
- [ ] **AI Recommendations**: ML-based profile suggestions
- [ ] **Horoscope Matching**: Full 10-porutham compatibility
- [ ] **Video Profiles**: Upload and view video introductions
- [ ] **Payment Integration**: Premium membership tiers
- [ ] **Admin Dashboard**: Manage users and profiles
- [ ] **Multi-language**: Tamil, Hindi, Telugu support

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🤝 Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📧 Contact

**Project Maintainer**: Pradeep
- Email: pradeepsubsun@gmail.com
- GitHub: [Your GitHub Profile]

---

## 🙏 Acknowledgments

- **Databricks** - For the unified analytics platform
- **Gradio** - For the easy-to-use UI framework
- **Tamil Astrology Community** - For star compatibility knowledge
- **Matrimony** - Original data source

---

## 📸 Screenshots

### Login & Signup
![Login Screen](screenshots/login.png)
![Signup with OTP](screenshots/signup.png)

### Main App
![Profile Filters](screenshots/filters.png)
![Profile List](screenshots/profiles.png)
![Full Profile View](screenshots/profile-detail.png)

### Compatibility
![Star Matching](screenshots/compatibility.png)

### Analytics
![Charts Dashboard](screenshots/charts.png)

---

<div align="center">

### Built with ❤️ on Databricks

**[⬆ Back to Top](#--matrimony-match-analyser)**

</div>
