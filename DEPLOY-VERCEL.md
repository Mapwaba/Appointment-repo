# Deploying the frontends to Vercel

The three apps deploy as **three Vercel projects from this one repo**. Each project uses a different Root Directory. They all call the API on Render, so set that up first by following `DEPLOY-RENDER.md` in [Medical-api-repo](https://github.com/Mapwaba/Medical-api-repo).

## Create the three projects

Do this three times, once per row: **Add New → Project → import `Mapwaba/Appointment-repo`**.

| Project name | Root Directory | Resulting URL |
|---|---|---|
| `landadoc-patient` | `src/Frontend/Patient` | https://landadoc-patient.vercel.app |
| `landadoc-doctor` | `src/Frontend/Doctor` | https://landadoc-doctor.vercel.app |
| `landadoc-admin` | `src/Frontend/Admin` | https://landadoc-admin.vercel.app |

For each project:
- Set **Root Directory** to the folder in the table. Click **Edit** next to it on the import screen.
- Set **Framework Preset** to **Other**, and leave the build and output settings empty. The `vercel.json` in each app folder provides them.
- Keep **Include files outside the root directory** turned on (it's the default). The build needs `src/Frontend/LandaDoc.Frontend.Shared`, `src/LandaDoc.Shared` and `deploy/`.
- Click **Deploy**.

The build script [deploy/vercel/build.sh](deploy/vercel/build.sh) runs for each app. It installs the .NET 9 SDK (about a minute), publishes the app, and replaces `appsettings.Production.json` with [deploy/vercel/appsettings.Production.json](deploy/vercel/appsettings.Production.json). That file holds the Render API URLs.

## Connect the apps to the API

The API only accepts browser calls from the origins it knows about. In Render, go to **Environment Groups → landadoc-shared** and set:

| Key | Value |
|---|---|
| `Cors__AllowedOrigins__0` | `https://landadoc-patient.vercel.app` |
| `Cors__AllowedOrigins__1` | `https://landadoc-doctor.vercel.app` |
| `Cors__AllowedOrigins__2` | `https://landadoc-admin.vercel.app` |

Use the real URLs if Vercel gave a project a different one. Also update `Frontend__PatientBaseUrl` on the Render **payment** service to the Patient URL. Then redeploy the Render services.

## Redeploying

Every push to `main` rebuilds all three projects. To rebuild only the app that changed, go to each Vercel project's **Settings → Git → Ignored Build Step** and use:

```
git diff HEAD^ HEAD --quiet -- . ../LandaDoc.Frontend.Shared ../../LandaDoc.Shared ../../../deploy
```
