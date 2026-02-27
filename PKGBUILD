# Maintainer: Your Name <your.email@example.com>
# Rodecaster Duo PipeWire virtual devices (Game, System, Chat, Music) - installable package

pkgname=rodecaster-duo-pipewire
pkgver=1.0.0
pkgrel=1
pkgdesc="PipeWire virtual sinks/sources for Rodecaster Duo (Game, System, Chat, Music)"
arch=(any)
url="https://github.com/rewalo/rodecaster-duo-pipewire"
license=('MIT')
depends=(pipewire pipewire-pulse pipewire-cli bash)
optdepends=('wireplumber: recommended session manager')
source=(
  '99-rodecaster-duo-virtual-sinks.conf.template'
  '99-rodecaster-duo-virtual-sources.conf.template'
  'install-rcp-duo-pipewire.sh'
  'discover-rcp-devices.sh'
  'rodecaster-duo-set-pro-audio.sh'
  'LICENSE'
)
sha256sums=(
  'SKIP'
  'SKIP'
  'SKIP'
  'SKIP'
  'SKIP'
  'SKIP'
)

package() {
  cd "$srcdir"
  install -d "$pkgdir/usr/share/rodecaster-duo-pipewire"
  install -m644 99-rodecaster-duo-virtual-sinks.conf.template \
    "$pkgdir/usr/share/rodecaster-duo-pipewire/"
  install -m644 99-rodecaster-duo-virtual-sources.conf.template \
    "$pkgdir/usr/share/rodecaster-duo-pipewire/"
  install -Dm755 install-rcp-duo-pipewire.sh \
    "$pkgdir/usr/bin/rodecaster-duo-pipewire-install"
  install -Dm755 discover-rcp-devices.sh \
    "$pkgdir/usr/bin/rodecaster-duo-pipewire-discover"
  install -Dm755 rodecaster-duo-set-pro-audio.sh \
    "$pkgdir/usr/bin/rodecaster-duo-set-pro-audio"
  install -Dm644 LICENSE "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}

post_install() {
  _run_for_user() {
    local u="$1"
    local uid
    uid=$(getent passwd "$u" 2>/dev/null | cut -d: -f3)
    [ -z "$uid" ] && return 0
    [ -d "/run/user/$uid" ] || return 0
    runuser -u "$u" -- env XDG_RUNTIME_DIR="/run/user/$uid" \
      /usr/bin/rodecaster-duo-set-pro-audio 2>/dev/null || true
    runuser -u "$u" -- env XDG_RUNTIME_DIR="/run/user/$uid" \
      /usr/bin/rodecaster-duo-pipewire-install 2>/dev/null || true
    runuser -u "$u" -- env XDG_RUNTIME_DIR="/run/user/$uid" \
      systemctl --user restart pipewire pipewire-pulse 2>/dev/null || true
  }
  if [ -n "$SUDO_USER" ]; then
    _run_for_user "$SUDO_USER"
  fi
  for u in $(getent passwd | awk -F: '$3 >= 1000 && $3 < 65534 {print $1}'); do
    [ -d "/home/$u" ] || continue
    [ "$u" = "$SUDO_USER" ] && continue
    _run_for_user "$u" || true
  done
}
