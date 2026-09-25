# LandaDoc frontends

The three web apps for LandaDoc, a platform that helps patients and doctors manage appointments. Each one is a standalone Blazor WebAssembly app that talks to the API in [Medical-api-repo](https://github.com/Mapwaba/Medical-api-repo).

| App | Project | Used by | Local URL |
|---|---|---|---|
| Patient | `src/Frontend/Patient` | Patients: book appointments, pay, manage documents and family members | http://localhost:5299 |
| Doctor | `src/Frontend/Doctor` | Doctors: schedule, appointments, reviews | http://localhost:5003 |
| Admin | `src/Frontend/Admin` | Administrators: clinics, doctor approval | http://localhost:5500 |

## Shared code

| Path | Contents |
|---|---|
| `src/Frontend/LandaDoc.Frontend.Shared` | API clients, auth, SignalR hub client and shared components, used by all three apps |
| `src/LandaDoc.Shared` | DTOs, events and enums. **This is a copy.** The master copy lives in Medical-api-repo. |

When the API changes a DTO or enum, copy Medical-api-repo's `src/LandaDoc.Shared` over this repo's copy.

## Running locally

Requires the .NET 9 SDK.

1. Start the API with `run-all.ps1` in Medical-api-repo.
2. Run the app you need:

```powershell
dotnet run --project src/Frontend/Patient/LandaDoc.Patient.csproj
dotnet run --project src/Frontend/Doctor/LandaDoc.Doctor.csproj
dotnet run --project src/Frontend/Admin/LandaDoc.AdminApp.csproj
```

Open `LandaDoc.Frontend.sln` to work on all three in Visual Studio. Local API addresses are in each app's `wwwroot/appsettings.json`.

## Deploying

- **Vercel (connects to the Render API):** see [DEPLOY-VERCEL.md](DEPLOY-VERCEL.md). You create one Vercel project per app, all from this repo.
- **Self-hosted (Docker + nginx):** build any app with `docker/Dockerfile.wasm`, for example:
  `docker build -f docker/Dockerfile.wasm --build-arg PROJECT_PATH=src/Frontend/Patient/LandaDoc.Patient.csproj -t landadoc-patient .`
  This uses each app's `wwwroot/appsettings.Production.json`, which points at `https://api.landadoc.fr`.
