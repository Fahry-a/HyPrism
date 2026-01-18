pkgname=hyprism
pkgver=1.0.17
pkgrel=1
pkgdesc="Hytale launcher with mod management and more"
arch=('x86_64' 'aarch64')
url="https://github.com/Fahry-a/HyPrism"
license=('MIT')
depends=(
  'gtk3'
  'webkit2gtk'
  'glibc'
  'gcc-libs'
)
makedepends=(
  'git'
  'go'
  'nodejs'
  'npm'
)
optdepends=(
  'jre21-openjdk: For running Hytale'
)
provides=("${pkgname}")
conflicts=("${pkgname}-bin" "${pkgname}-git")
source=("${pkgname}-${pkgver}.tar.gz::https://github.com/Fahry-a/HyPrism/archive/refs/tags/v${pkgver}.tar.gz")
sha256sums=('SKIP')

prepare() {
  cd "HyPrism-${pkgver}"
  mkdir -p "${srcdir}/go"
}

build() {
  cd "HyPrism-${pkgver}"

  export GOPATH="${srcdir}/go"
  export GOBIN="${GOPATH}/bin"
  export PATH="${GOBIN}:${PATH}"
  export GOFLAGS="-buildmode=pie -trimpath -ldflags=-linkmode=external"
  export CGO_CPPFLAGS="${CPPFLAGS}"
  export CGO_CFLAGS="${CFLAGS}"
  export CGO_CXXFLAGS="${CXXFLAGS}"
  export CGO_LDFLAGS="${LDFLAGS}"

  cd frontend
  npm ci
  npm run build
  cd ..

  go install github.com/wailsapp/wails/v2/cmd/wails@latest

  local _wails_arch=""
  case "$CARCH" in
    x86_64) _wails_arch="linux/amd64" ;;
    aarch64) _wails_arch="linux/arm64" ;;
  esac

  wails build \
    -platform "${_wails_arch}" \
    -ldflags "-X 'app.Version=${pkgver}' -s -w" \
    -clean
}

package() {
  cd "HyPrism-${pkgver}"

  install -Dm755 "build/bin/${pkgname}" "${pkgdir}/usr/bin/${pkgname}"
  install -Dm644 LICENSE -t "${pkgdir}/usr/share/licenses/${pkgname}/"
  install -Dm644 README.md -t "${pkgdir}/usr/share/doc/${pkgname}/"

  local _icon=""
  if [ -f "build/appicon.png" ]; then
    _icon="build/appicon.png"
  elif [ -f "assets/icon.png" ]; then
    _icon="assets/icon.png"
  fi
  [ -n "${_icon}" ] && install -Dm644 "${_icon}" "${pkgdir}/usr/share/pixmaps/${pkgname}.png"

  cat > "${srcdir}/${pkgname}.desktop" << EOF
[Desktop Entry]
Name=HyPrism
Comment=${pkgdesc}
Exec=${pkgname}
Icon=${pkgname}
Terminal=false
Type=Application
Categories=Game;
Keywords=Hytale;Launcher;
StartupNotify=true
EOF
  install -Dm644 "${srcdir}/${pkgname}.desktop" "${pkgdir}/usr/share/applications/${pkgname}.desktop"
}