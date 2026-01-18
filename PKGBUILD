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
  
  # Create go directory if it doesn't exist
  mkdir -p "${srcdir}/go"
}

build() {
  cd "HyPrism-${pkgver}"

  # Set up Go environment
  export GOPATH="${srcdir}/go"
  export GOBIN="${GOPATH}/bin"
  export PATH="${GOBIN}:${PATH}"
  export GOFLAGS="-buildmode=pie -trimpath -ldflags=-linkmode=external"
  export CGO_CPPFLAGS="${CPPFLAGS}"
  export CGO_CFLAGS="${CFLAGS}"
  export CGO_CXXFLAGS="${CXXFLAGS}"
  export CGO_LDFLAGS="${LDFLAGS}"

  # Build frontend
  cd frontend
  npm ci
  npm run build
  cd ..

  # Install wails to local GOPATH
  go install github.com/wailsapp/wails/v2/cmd/wails@latest

  # Determine architecture for wails build
  local _wails_arch=""
  case "$CARCH" in
    x86_64) _wails_arch="linux/amd64" ;;
    aarch64) _wails_arch="linux/arm64" ;;
  esac

  # Build with wails
  wails build \
    -platform "${_wails_arch}" \
    -ldflags "-X 'app.Version=${pkgver}' -s -w" \
    -clean \
    -o "${pkgname}"
}

package() {
  cd "HyPrism-${pkgver}"

  # Install binary
  install -Dm755 "${pkgname}" "${pkgdir}/usr/bin/${pkgname}"

  # Install license
  install -Dm644 LICENSE -t "${pkgdir}/usr/share/licenses/${pkgname}/"

  # Install documentation
  install -Dm644 README.md -t "${pkgdir}/usr/share/doc/${pkgname}/"

  # Install icon
  local _icon=""
  if [ -f "build/appicon.png" ]; then
    _icon="build/appicon.png"
  elif [ -f "assets/icon.png" ]; then
    _icon="assets/icon.png"
  fi
  [ -n "${_icon}" ] && install -Dm644 "${_icon}" "${pkgdir}/usr/share/pixmaps/${pkgname}.png"

  # Create and install desktop file
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
