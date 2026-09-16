# libera-llrf2

Firmware-upgrade variant of [`../libera-llrf`](../libera-llrf) (added in 48e1c34,
"added libera-llrf with firmware upgrade"). `st.cmd.j2` and `start.sh.j2` are
byte-identical to `libera-llrf` - only two `db/` files differ, reflecting the
newer LLRF application's MCII layout:

- `db/adllr.db`: interlock channel PVs renumbered/renamed (e.g.
  `interlock:ch2:enabled` -> `interlock:ch1:enable`) and remapped to
  `channel_mask.interlock_enable.chN` instead of `interlock.ilk_level.chN.enable`.
- `db/application.db`: adds an `interlock:state:alarm` `bi` record (MAJOR on
  `interlock:state` going non-zero) not present in `libera-llrf`.

Use this template (`template: libera-llrf2`) for units running the newer
firmware; use `libera-llrf` for units still on the older one. There is
currently no facility config referencing `libera-llrf2` in
epik8s-btf/epik8-sparc/epik8s-euaps - this directory exists ahead of the
first unit being upgraded.
