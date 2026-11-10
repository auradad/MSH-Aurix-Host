# MSH locked images (bypass-proof)

On stock Pterodactyl/yolks images a server owner with file access can always delete or replace `MSH.jar` (or the
startup launcher) and get a normal always-on server, because every file in `/home/container` is theirs and
Calagopus forces the startup to a single command pointing at one of those files.

These images fix that. The MSH launcher (`msh-boot`) and a fallback `MSH.jar` are baked into the Docker image at
`/usr/local/bin/msh-boot` and `/opt/msh/MSH.jar` — **outside** `/home/container`. The egg's startup command is the
single word `msh-boot`, which owners cannot edit. There is no file in the owner's reach that launches the server, so
MSH cannot be bypassed. On every boot `msh-boot` pulls your latest `MSH.jar` from the release into `/tmp` and runs it;
if that download fails, it runs the copy baked into the image — never a file the owner can write.

## One-time setup

1. **Upload `MSH.jar` to the `AurixHost` release** (obfuscated build). The image build downloads it from there.
2. **Build the images.** Push this repo (with `docker/` and `.github/workflows/images.yml`) to
   `github.com/auradad/MSH-Aurix-Host`, then run the **Build MSH images** workflow (Actions tab → Run workflow), or
   just (re)publish the release. It builds `ghcr.io/auradad/msh:java_8 … java_25` and pushes them.
3. **Make the packages public** so your nodes can pull them without login: GitHub → your profile → Packages → each
   `msh` package → Package settings → Change visibility → Public. (Or configure registry auth on the nodes.)
4. **Import the locked eggs** in Calagopus: `egg/egg-paper-msh-locked.json` and `egg/egg-forge-msh-locked.json`.
   Create servers with them (or switch existing servers to the locked egg and Reinstall).

## Updating MSH later

Replace the `MSH.jar` asset on the release and re-run the workflow (to refresh the baked fallback). Servers pick up
the new jar on their next start via the `/tmp` download; the rebuild only refreshes the offline fallback.

## Notes

- Owners still can't change RAM, port, startup, or the MSH variables — same as the normal eggs.
- Bedrock/Geyser, `msh.config`, host-lock, idle timeout: all identical to the normal eggs. Only the launch path differs.
- The non-locked eggs (`egg-paper-msh.json`, `egg-forge-msh.json`) still work on stock images if you don't need the
  bypass protection; they're easier to set up but an owner can opt out of MSH.
