# MedFlow 🩺

![MedFlow](docs/images/medflow.png)

MedFlow is a local-first consultation support system created by a six-student Software Engineering team at the University of Southern Denmark. It was designed for a potential local hospital or outpatient clinic in Sønderborg. The system records outpatient consultations, transcribes them locally, produces structured clinical draft material, and keeps the doctor in control of every approval.

Sensitive consultation data stays within the local deployment. AI output is advisory and editable; nothing is accepted as clinical content without explicit doctor approval.

## What It Does

- Records consultation audio and transcribes it with Faster-Whisper.
- Generates structured draft reports through Ollama and Qwen3:8b.
- Supports review, editing, approval, PDF export, email follow-up, and auditability.
- Uses MySQL for relational records and MongoDB for flexible AI artifacts.

## Run Locally

Requirements: Docker Desktop. An NVIDIA GPU is optional; the transcription service falls back to CPU.

Windows users should ensure Docker Desktop is running. GPU acceleration also requires the NVIDIA Container Toolkit.

```bash
git clone https://github.com/MRM-MB/MEDFLOW.git
cd MEDFLOW
cp .env.example .env
docker compose up -d --build
docker compose exec ollama ollama pull qwen3:8b
```

Open the application at <http://localhost:8000> and the local MailHog inbox at <http://localhost:8025>.

The first build downloads the Faster-Whisper `large-v3` model. The Ollama command downloads Qwen3:8b once and stores it in the Docker volume.

The Docker stack starts six services: `app`, `whisper`, `mysql`, `mongodb`, `ollama`, and `mailhog`.

### Demo Access

All demo accounts use the password `password`.

| Role | Email |
| --- | --- |
| Doctor | `doctor@example.local` |
| Admin | `admin@example.local` |
| Patient | `giulia@example.local` |

The project ships with pre-seeded users, appointments, and consultations. Additional demo patients are available after sign-in.

## Explore the Workflow

- **Doctor:** start a consultation, record audio, generate a report, review or edit the draft, approve it, and issue a PDF prescription.
- **Admin:** manage staff, patients, appointments, configuration, and audit records.
- **Patient:** view consultation history and prescriptions, and book appointments.

MailHog captures outgoing prescription and notification email locally, so no external SMTP service is required for the demo.

## Architecture

![MedFlow local runtime architecture](docs/images/architecture.png)

The FastAPI application coordinates MySQL, MongoDB, an Ollama local LLM, and a Faster-Whisper sidecar. The code follows Clean Architecture boundaries, allowing the infrastructure integrations to evolve independently.

## Local AI and Validation

![Ollama model comparison](docs/images/ollama_models.png)

Qwen3:8b was selected for reliable structured output within the available local hardware budget. The project includes unit, integration, smoke, stress, benchmark, and UI validation.

![Automated test distribution](docs/images/tests_count.png)

Run the automated suite inside the application container:

```bash
docker compose exec app pytest
```

## Configuration and Operations

`.env.example` provides working defaults. `USE_MOCK_ADAPTERS=true` uses the supplied in-memory demo data; set it to `false` only when configuring persistent MySQL and MongoDB writes. `WHISPER_MODEL_NAME`, `LLM_MODEL_NAME`, `SMTP_HOST`, `SMTP_PORT`, and `SECRET_KEY` are also configured there.

```bash
# Stop services while preserving Docker volumes
docker compose down

# Restart without rebuilding
docker compose up -d

# Reset all local data and volumes
docker compose down -v
```

If a service fails to start, first confirm that Docker Desktop is running. Use `docker compose logs app` for application errors and `docker compose logs whisper` to confirm that the transcription model initialized. If ports are busy, stop local MySQL or MongoDB instances using ports `3306` or `27017`.

## Project Material

- `docs/` contains the diagrams and supporting documentation.
- `Validation_Reports/` contains the validation evidence and charts.
- `overleaf-report/` contains the source for the project report.

## 👥 Contributors

<table width="100%">
  <thead>
    <tr>
      <th>Name</th>
      <th>GitHub Profile</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><b>Luigi</b></td>
      <td><a href="https://github.com/Lucol24">Lucol24</a></td>
    </tr>
    <tr>
      <td><b>Aleksandra</b></td>
      <td><a href="https://github.com/Kwiatek05">Kwiatek05</a></td>
    </tr>
    <tr>
      <td><b>Gabriele</b></td>
      <td><a href="https://github.com/Gabbo693">Gabbo693</a></td>
    </tr>
    <tr>
      <td><b>Gabija</b></td>
      <td><a href="https://github.com/GabijaSt">GabijaSt</a></td>
    </tr>
    <tr>
      <td><b>Mats</b></td>
      <td><a href="https://github.com/mqts241">mqts241</a></td>
    </tr>
    <tr>
      <td><b>Manish</b></td>
      <td>-</td>
    </tr>
  </tbody>
</table>
