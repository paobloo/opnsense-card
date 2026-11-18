<p>
  ☕ <strong>If you find this card useful, you can support the project:</strong>
</p>

<div style="white-space: nowrap; line-height: 0;">
  <a
    href="https://www.buymeacoffee.com/Marlboro62"
    target="_blank"
    rel="noopener noreferrer"
    style="display: inline-block; text-decoration: none; vertical-align: middle;"
  ><img
    src="https://img.buymeacoffee.com/button-api/?text=Buy%20me%20a%20coffee&emoji=%E2%98%95&slug=Marlboro62&button_colour=FF5F5F&font_colour=ffffff&font_family=Cookie&outline_colour=000000&coffee_colour=FFDD00"
    alt="Buy me a coffee on Buy Me a Coffee"
    height="45"
    style="display: block; border: 0;"
  ></a>&nbsp;&nbsp;<a
    href="https://ko-fi.com/M4M2QL45X"
    target="_blank"
    rel="noopener noreferrer"
    style="display: inline-block; text-decoration: none; vertical-align: middle;"
  ><img
    src="https://storage.ko-fi.com/cdn/kofi6.png?v=6"
    alt="Support the project on Ko-fi"
    height="45"
    style="display: block; border: 0;"
  ></a>
</div>

# OPNsense Card

[![HACS Default](https://img.shields.io/badge/HACS-Default-41BDF5.svg)](https://hacs.xyz/)
[![Release](https://img.shields.io/github/v/release/Marlboro62/opnsense-card)](https://github.com/Marlboro62/opnsense-card/releases)

Lovelace card for monitoring a firewall **OPNsense** to Home Assistant : gateways, interface traffic, resources, updates, and reboot. It has the same style as the MyElectricalData new ([Carte Enedis Linky New](https://github.com/Marlboro62/content-card-linky-new)) et des [Cartes Serveur HA](https://github.com/Marlboro62/serveur-ha-card) : midnight blue theme, light version, or Home Assistant theme.

**No dependencies** : pas de button-card ni de card-mod. Sensors are **detected automatically**, and any element without a sensor is simply hidden.

| Normal | Under load, update available | WAN offline |
| --- | --- | --- |
| ![OPNsense](https://raw.githubusercontent.com/Marlboro62/opnsense-card/main/images/opnsense.png) | ![OPNsense under load](https://raw.githubusercontent.com/Marlboro62/opnsense-card/main/images/opnsense-charge.png) | ![WAN offline](https://raw.githubusercontent.com/Marlboro62/opnsense-card/main/images/opnsense-wan.png) |

| Light, on PC | Without integration (REST package) |
| --- | --- |
| ![OPNsense light](https://raw.githubusercontent.com/Marlboro62/opnsense-card/main/images/opnsense-clair.png) | ![Package REST](https://raw.githubusercontent.com/Marlboro62/opnsense-card/main/images/opnsense-rest.png) |

*Previews created using the demo and fictional values (invented names, IP addresses, and traffic rates).*

**[▶ Try the online demo](https://marlboro62.github.io/opnsense-card/docs/demo.html)** : pare-feu normal, chargé avec une mise à jour, ou WAN offline, avec l'intégration ou le package REST, en sombre ou clair, sur téléphone ou PC.

## What it displays

- **Header** : name and version, Internet status, indicators **Gateways** (red if a gateway is offline), **Update** et **Alerts** (pending notifications in OPNsense). The card gets a red border when a gateway goes down.
- **Information** : uptime, installed or available version, baux DHCP, CPU load.
- **Resources** : CPU, RAM (used / total), disk, pf state table, network buffers (mbuf), swap, and temperatures reported by OPNsense.
- **Gateways** : status (online, latency, packet loss, offline), latency, packet loss, and jitter, with WAN first.
- **Interfaces** : receive and transmit rate for each interface, en kbit/s, Mbit/s ou Gbit/s, and disconnected interfaces shown in red.
- **Actions** : **Reboot** et **Shut down** the firewall; **a second click within 5 seconds** is required to confirm.

Clicking an element opens the entity details in Home Assistant.

## Two ways to obtain the data

| | OPNsense integration (HACS) | REST package, without integration |
| --- | --- | --- |
| Installation | through the Home Assistant interface, without YAML | one YAML file and two lines in `secrets.yaml` |
| Gateways et interfaces | toutes, détectées | toutes, détectées |
| Temperatures | all sensors | the first sensor |
| DHCP leases, alerts | yes | no |
| Reboot, arrêter | oui (selon les privilèges) | en option (`rest_command`) |

**Tip** : the integration is the simplest and most complete option. Use the package if you do not want to, or cannot, install the HACS integration. Do not use both at the same time (this would create duplicate sensors).

> The card does not call the OPNsense API directly: the browser would block these requests (CORS, self-signed certificate), and the API key would be readable by anyone opening the dashboard. Home Assistant always queries OPNsense.

## Installing the card

### With HACS (recommended)

1. In HACS, search for **Carte OPNsense** and install it.
2. Refresh the page (Ctrl+F5).

If the card does not appear in HACS search yet, add the repository manually: menu **⋮** → **Custom repositories**, URL `https://github.com/Marlboro62/opnsense-card`, type **Tableau de bord** (Dashboard), then search for **Carte OPNsense**.

### Manually

1. Download [`opnsense-card.js`](https://raw.githubusercontent.com/Marlboro62/opnsense-card/main/opnsense-card.js) (right-click → Save as) and copy it to `/config/www/`.
2. **Paramètres → Tableaux de bord → ⋮ → Resources → Ajouter une ressource** : URL `/local/opnsense-card.js`, type **JavaScript module**.
3. Refresh the page (Ctrl+F5).

Then add the card:

```yaml
type: custom:opnsense-card
```

The card has a **visual editor**.

## With the OPNsense integration (HACS)

1. In OPNsense, **System → Access → Users** : create a user for Home Assistant and its **clé d'API** (key icon). The required privileges are described in the [documentation de l'intégration](https://github.com/travisghansen/hass-opnsense#readme).
2. In HACS, install **OPNsense** ([travisghansen/hass-opnsense](https://github.com/travisghansen/hass-opnsense)), then restart Home Assistant.
3. **Settings → Devices & services → Add integration → OPNsense** : URL du pare-feu, clé et secret.

The card automatically detects the firewall; if you have multiple firewalls, select one in the editor (`device`). Les interface traffic rates et l'état des passerelles sont activés par défaut to l'intégration ; les compteurs (octets, paquets, erreurs) et les autres disks sont désactivés par défaut, la carte n'en a pas besoin.

## Without integration: the REST package

Le fichier [`packages/opnsense.yaml`](packages/opnsense.yaml) creates sensors using Home Assistant's built-in REST integration. **Seule l'URL du pare-feu est à indiquer** : toutes les passerelles et toutes les interfaces are read automatically, and the card calculates traffic rates itself.

1. **Dans OPNsense**, **System → Access → Users** : create a dedicated user (for example `homeassistant`), with these privileges (use the search field to find them; names may be translated):

   | Privilege | Purpose |
   | --- | --- |
   | `Lobby: Dashboard` | mémoire, disk, table d'états, tampons, swap, température, uptime, charge |
   | `Diagnostics: System Activity` | CPU |
   | `Reporting: Traffic` | interface traffic rates |
   | `System: Gateways` | gateway status, latency, and packet loss |
   | `System: Firmware` (optional) | version et mise à jour |

   `System: Gateways` et `System: Firmware` also allow you to **modify** gateways and start an update: without `System: Firmware`, remove the “OPNsense version” block from the package. Then create the **clé d'API** de cet utilisateur (key icon) : OPNsense downloads a file containing the key and secret.
2. **Dans `/config/secrets.yaml`** :

   ```yaml
   opnsense_api_key: "the key"
   opnsense_api_secret: "the secret"
   ```

3. Copy [`opnsense.yaml`](packages/opnsense.yaml) to `/config/packages/`, et activez les packages to `configuration.yaml` if you have not already done so:

   ```yaml
   homeassistant:
     packages: !include_dir_named packages
   ```

4. In the file, replace every occurrence of `https://192.168.1.1` par l'URL de votre OPNsense (« Remplacer tout »).
5. **Developer Tools → YAML → Check configuration**, then restart Home Assistant.

The card automatically detects these sensors. Traffic rates appear after the second reading (15 seconds), or immediately if the history of sensor `sensor.opnsense_traffic` already exists.

A gateway's latency, packet loss, and jitter are displayed only if its **surveillance** est active to OPNsense (**Système → Gateways → Configuration**, uncheck “Disable Gateway Monitoring” and specify a **monitoring IP**, for example `1.1.1.1`) ; otherwise the card displays “not monitored”.

**Reboot et Shut down** : the buttons appear only if you uncomment the block `rest_command` at the bottom of the package and grant the user the privileges `Diagnostics: Reboot System` et `Diagnostics: Halt system`. Otherwise, the API user cannot shut down the firewall.

Les URLs de l'API du package sont celles d'OPNsense 25.7 et plus récent ; pour une version plus ancienne, le haut du fichier indique les noms à changer.

## Options

| Option | Purpose | Default |
| --- | --- | --- |
| `title`, `icon`, `icon_color` | Header | nom du pare-feu, `mdi:wall-fire` |
| `device` | Displayed firewall (integration) | the first one found |
| `prefix` | REST package: prefix of sensor names, if you renamed them | `opnsense` |
| `interfaces` | Interfaces affichées (nom, for example `WAN`, `LAN`) | all |
| `show_info`, `show_resources`, `show_gateways`, `show_interfaces`, `show_actions` | Show each section | `true` |
| `theme` | `v2` (MyElectricalData midnight blue), `auto` (light or dark depending on Home Assistant) ou `ha` (Home Assistant theme) | `v2` |

Example:

```yaml
type: custom:opnsense-card
title: Firewall
interfaces:
  - WAN
  - LAN
show_actions: false
```

## Support

Questions and issues: use only the [issues GitHub](https://github.com/Marlboro62/opnsense-card/issues).

## See also

[Cartes Serveur HA](https://github.com/Marlboro62/serveur-ha-card) · [Carte Onduleur UPS](https://github.com/Marlboro62/onduleur-ups-card) · [Carte Enedis Linky New](https://github.com/Marlboro62/content-card-linky-new)

## License

MIT

## Project name and forks

This repository is the only official source for **OPNsense Card** (releases, HACS, support),
maintained by [Marlboro62](https://github.com/Marlboro62).

The MIT license allows you to copy, modify, and redistribute the code. If you publish a modified version:

- keep the copyright notices in the file [`LICENSE`](https://github.com/Marlboro62/opnsense-card/blob/main/LICENSE) (© Marlboro62) ;
- **give it a different name** and different card types (`custom:opnsense-card` are the ones used by this repository) :
  Home Assistant ne peut pas charger deux cartes du même type, et personne ne doit confondre les deux versions to HACS ;
- do not present it as the official version or direct requests to this repository
  for support relating to your version.

A fix or an idea? **Pull requests** and **issues** are welcome here: **pull requests** et les **issues** sont les bienvenues ici :
they benefit all users.
