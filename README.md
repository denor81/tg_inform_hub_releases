# Notification Hub releases

## Быстрая установка

Для установки последней стабильной версии выполните одну команду:

```bash
curl -fsSL https://raw.githubusercontent.com/denor81/tg_inform_hub_releases/main/install.sh | sudo bash
```

GitHub token не нужен. Bootstrap автоматически скачает последний stable-релиз,
проверит его GitHub/Sigstore attestations, manifest, SHA-256 и содержимое архива
и только после этого запустит установщик.

Для последующих обновлений установленного Hub используйте:

```bash
sudo notify-hub update apply
```

## Расширенная инструкция

Более осторожный вариант сохраняет bootstrap в файл: его можно предварительно
просмотреть, вывести список релизов и только затем запустить от root.

```bash
curl --fail --silent --show-error --location \
  --output /tmp/install-notification-hub.sh \
  https://raw.githubusercontent.com/denor81/tg_inform_hub_releases/main/install.sh
chmod 0755 /tmp/install-notification-hub.sh
/tmp/install-notification-hub.sh --list
sudo /tmp/install-notification-hub.sh
```

По умолчанию устанавливается последний stable-релиз. Можно выбрать точную
стабильную версию или явно разрешить release candidate:

```bash
sudo /tmp/install-notification-hub.sh --version v0.3.1
sudo /tmp/install-notification-hub.sh --channel rc
sudo /tmp/install-notification-hub.sh --version v0.3.0-rc.2
```

Bootstrap скачивает checksum-pinned GitHub CLI verifier, проверяет GitHub
Artifact Attestation манифеста до доверия указанному в нём digest, затем digest
и attestation архива и каждый archive entry по strict manifest. Release-код не
запускается до завершения всех проверок.

Если Hub уже установлен в A/B layout, public bootstrap намеренно не обходит
транзакционный updater. Используйте точную версию при необходимости:

```bash
sudo notify-hub update check
sudo notify-hub update apply --version v0.3.1
sudo notify-hub update status
```

## Удаление

Показать план, удалить только программу с сохранением данных или выполнить
полное необратимое удаление:

```bash
sudo notify-hub uninstall --dry-run
sudo notify-hub uninstall
sudo notify-hub uninstall --purge-all
```

Обычное удаление сохраняет конфигурацию, базу, очередь, credentials и системного
пользователя для безопасной повторной установки. `--purge-all` дополнительно
удаляет их и требует ввести точное подтверждение `DELETE notification-hub`.
Ubuntu packages и внешняя конфигурация Nginx не удаляются.

## О репозитории

Это отдельный публичный binary release repository
`denor81/tg_inform_hub_releases`. Он содержит только минимальный README,
bootstrap, защищённый attestation workflow, immutable release artifacts, strict
manifests и GitHub Artifact Attestation bundles. Здесь никогда не должны
появляться конфигурация Hub, credentials, базы данных, логи, уведомления или
история приватных исходников.

Canonical-копии публичных файлов поддерживаются в приватном исходном
репозитории: `release-channel/README.md`, `release-channel/install.sh` и
`release-channel/attest-release.yml`. Последний копируется в публичный путь
`.github/workflows/attest-release.yml`.
