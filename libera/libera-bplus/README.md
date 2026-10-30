# libera-bplus

Template for **Libera Brilliance+** eBPM units (MCI application `libera-ebpm`),
derived from the vendor IOC shipped in `/opt/libera-ioc` on the unit.
Not to be confused with [`../libera-spe`](../libera-spe) (Single Pass E,
`libera-spe` application, `rfspe` boards) or [`../libera-sppp`](../libera-sppp).

`db/` and `dbd/` are the vendor files unchanged (same liberaIOC build as
`libera-spe`). `st.cmd.j2` loads:

- `ebpm.db`, `gdx.db` (gdx1), `tim.db` (tim2), `evrx.db` (evrx2) on `libera-ebpm`
- one `raf.db` per entry in `devices`, board `raf<channel>` (slots 3..6)
- platform sensors (os, icb0, gdx1, evrx2, tim2, and `raf_sensors.db` per device)

`gdx1` and `tim2` are not fitted on every unit (check with
`/opt/libera/bin/libera-ireg dump -l 1 boards`): set `gdx: false` and/or
`tim: false` on the ioc to skip their records (default: loaded).

PV names: `<iocprefix>:<dev.name>:...` for each BPM (a device-level `prefix`
overrides `iocprefix`), `<iocprefix>:gdx|tim|evrx:...` for the shared boards.

Example:

```yaml
- name: "libera12"
  template: "libera-bplus"
  devtype: bpm
  devgroup: diag
  iocprefix: "XXX:DIA:LIB12"
  iocbin: "/opt/libera-ioc/bin/liberaIOC"
  devices:
    - name: "BPM01"
      channel: 3
    - name: "BPM02"
      channel: 4
    - name: "BPM03"
      channel: 5
    - name: "BPM04"
      channel: 6
  host: "libera12"
  ssh_options: "-o HostKeyAlgorithms=+ssh-rsa -o PubkeyAcceptedKeyTypes=+ssh-rsa"
  scpoptions: "-O"
  exec: "start.sh"
```
