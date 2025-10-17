# Transformer Maintenance System

A modern web platform for power transformer maintenance, combining AI-powered thermal imaging, inspection management, and digital record-keeping.

## 🚀 Overview

The **Transformer Maintenance System (TMS)** helps operators and technical officers monitor, inspect, and maintain transformers efficiently. The system uses **AI-powered thermal image analysis** to detect faults, supports manual validation, and maintains a complete history of maintenance actions.

## 🟢 Phase 1: Transformer & Baseline Image Management

*Current Focus:*
- Manage transformer records (add, edit, view, list, delete)
- Manage inspection records (add, edit, view, list, delete)
- Upload, store, view, and delete baseline and inspection thermal images for each transformer

## ✨ Implemented Features

### Transformer Management
- **Complete CRUD Operations**: Add, view, edit, list, and delete transformer records
- **Metadata Storage**: All transformer data stored in PostgreSQL relational database
- **Comprehensive Records**: Track transformer specifications, location, and operational data

### Inspection Management
- **Inspection Lifecycle**: Add, view, edit, list, and delete inspections for each transformer
- **Inspection Records**: Associate multiple inspection records with each transformer entry
- **Latest Inspection Dashboard**: Dedicated page showing the most recent inspection record for each transformer
- **Historical Tracking**: Maintain complete inspection history for each transformer

## 🟣 Phase 2: Automated Anomaly Detection

In Phase 2 we added an AI-based engine and utilities to automatically detect thermal anomalies by comparing new (maintenance) thermal images with baseline images. The implementation lives primarily in the `phase2_fault_type/` folder and is designed to be modular so it can be integrated with the web application.

### Overview

The Phase 2 pipeline compares baseline and maintenance image pairs using an unsupervised image-difference method, runs a YOLO detector on the maintenance image to identify fault types, and fuses both outputs (IoU-based) so anomaly regions can be labeled with a fault type and confidence. Visual overlays and JSON outputs are produced for downstream display in the frontend.

### Functionality

- AI-Based Anomaly Detection Engine
  - Compares maintenance images with baseline images of the same transformer using image registration, histogram matching, and a fused difference map (AbsDiff + (1 − SSIM)).
  - Detects thermal anomalies such as hotspots, asymmetries, and changes in hotspot locations.
  - Thresholding mechanism is supported (percentile-based or fixed) to flag anomaly regions.

- Side-by-Side Image Comparison View (Frontend)
  - The pipeline produces overlay visualizations (PNG) and structured JSON that can be consumed by the frontend to render side-by-side comparisons with zoom/pan controls and highlighted anomaly regions (bounding boxes, heatmaps, or masks).

- Automatic Anomaly Marking
  - When anomalies are detected the pipeline annotates regions with color-coded overlays and exports metadata including pixel coordinates, region area/score, and a fused `fault_confidence` when a YOLO detection matches the region.
  - A confidence field is included for detections below thresholds so uncertain cases can be flagged for human review.

### Quick start (single pair)

1) Install Python dependencies (recommended in a venv) from the pipeline requirements:

```powershell
pip install -r phase2_fault_type\requirements.txt
```

2) Ensure a YOLO weights file is present (or use the existing `phase2_fault_type/weights/best.pt`).

3) Run the model for inference pipeline for a baseline/maintenance pair:

```
python phase2_fault_type/api/inference_api.py`
```

Outputs will include a fused JSON containing unsupervised regions enriched with `fault_type`, `fault_confidence`, and IoU metrics, and an optional overlay PNG visualizing anomalies and labels.

### Deliverables & Notes

- The `phase2_fault_type/` module has been integrated with the frontend so the web UI can show side-by-side images with overlays, zoom/pan, and metadata panels.
- Performance: The pipeline runs on CPU by default and will use CUDA if available through Ultralytics. There are flags and parameters to tune inference and thresholding speed/quality.
- Storage: Save pipeline JSON outputs to a backend storage endpoint for later retrieval and user feedback.


## � Getting Started

### Prerequisites
- **Java 21** or higher
- **Node.js 18+** and npm
- **PostgreSQL 15+**
- **Maven 3.8+**

### 1. Clone the Repository

```bash
git clone <repository-url>
cd transformer-maintenance-system-phase-02
```

### 2. Database Setup

```sql
CREATE DATABASE tms_db;
```

Update `tms-backend-application/src/main/resources/application.properties`:
```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/tms_db
spring.datasource.username=your_username
spring.datasource.password=your_password
```

### 3. Backend Setup

```bash
cd tms-backend-application
mvn clean install
mvn spring-boot:run
```

### 4. Frontend Setup

```bash
cd tms-frontend-application
npm install
npm run dev
```

### 5. Access the Application

- **Frontend**: http://localhost:5173
- **Backend API**: http://localhost:8080/api

## ☁️ Cloudinary Setup

Cloudinary stores and manages transformer images (baseline + inspection).

**Setup Steps:**
1. Sign up at [cloudinary.com](https://cloudinary.com/)
2. Copy **Cloud Name** from your dashboard
3. Create an upload preset:
   - Go to **Settings → Upload → Upload Presets → Add Upload Preset**
   - Set **Signing Mode** to `Unsigned` and save
4. Add credentials to your frontend `.env` file

## 🚢 Deployment

### Production Deployment
- **Backend**: Deploy as Spring Boot application (JAR file or Docker container)
- **Frontend**: Build with `npm run build` and serve static files via web server (Nginx/Apache)
- **Database**: Ensure PostgreSQL is running and accessible with proper security configurations

### Environment Considerations
- Update environment variables for production URLs
- Configure CORS settings appropriately
- Set up SSL certificates for HTTPS
- Configure proper database connection pooling

## 🛠️ Technology Stack

- **Frontend**: React, Vite, JavaScript/TypeScript
- **Backend**: Spring Boot, Java 21
- **Database**: PostgreSQL
- **Cloud Storage**: Cloudinary
- **Build Tools**: Maven (backend), npm (frontend)

## 🔧 Development

### Running in Development Mode
```bash
# Backend (runs on port 8080)
cd tms-backend-application
mvn spring-boot:run

# Frontend (runs on port 5173)
cd tms-frontend-application
npm run dev
```

### Building for Production
```bash
# Backend
cd tms-backend-application
mvn clean package

# Frontend
cd tms-frontend-application
npm run build
```

## 🚧 Current Limitations

- **Authentication & Authorization**: No user accounts, roles, or access control (all operations are unrestricted)
- **Security Hardening**: No rate limiting, content security policy, or request size constraints configured
- **Deployment**: No IaC scripts (Terraform/Docker Compose) provided
- **Mobile Optimization**: Layout not fully optimized for narrow devices

## 📄 License

This project is for **academic and demonstration purposes**.

## 👥 Authors

- **210325M** - Kuruppu M.P.
- **210349N** - Madhushan I.D.
- **210371A** - Manatunge J.M.
- **210463H** - Perera L.C.S.

---

*For issues or contributions, please contact the development team.*