# Deploy the Meter Reading Dashboard to Azure Static Web Apps

This folder is the deploy package:
- `index.html` — the dashboard (self-contained)
- `staticwebapp.config.json` — locks the site to **invited users only** (the 4 readers)

The config requires the **`reader`** role, so the page is private until you invite people — nobody
can see customer/meter data without an invitation. No Entra app registration needed.

## One-time setup (~15 min, IT)

**1. Create the Static Web App**
- Azure portal → **Static Web Apps** → **Create**
- Subscription/Resource group: your choice · Name: e.g. `edge-meter-board`
- Plan type: **Free**
- Deployment source: **Other**
- Create. When it finishes, open the resource → **Overview** → **Manage deployment token** → copy the token.

**2. Deploy the files** (from this folder, on a machine with Node)
```
npm install -g @azure/static-web-apps-cli
cd "azure-dashboard-deploy"
swa deploy . --env production --deployment-token <PASTE_TOKEN>
```
The CLI prints the live URL, e.g. `https://edge-meter-board.azurestaticapps.net`.

**3. Invite the 4 readers** (this is what restricts access)
- SWA resource → **Role management** → **Invite**
- Identity provider: **Azure Active Directory** (so they use EDGE credentials)
- Enter each person's email, role = **`reader`**, generate the invite link, send it.
- They click the link, sign in, and get access. Only invited `reader` accounts can open the board.

**4. Share the URL** with the team. Done.

> To open it to the whole EDGE tenant instead of 4 named people, tell me and I'll swap the config to
> tenant-wide Entra auth (requires a one-time app registration).

## Keeping it current
The page is a snapshot. To refresh: get the updated `index.html` from Claude (the morning auto-refresh
can produce it), drop it in this folder, and re-run the `swa deploy` line in step 2. Point the deploy
at a synced folder + a scheduled script and it republishes hands-free.

## Embed in Notion (optional)
After it's live, add an **Embed** block on the Notion page with the URL; viewers still authenticate.
