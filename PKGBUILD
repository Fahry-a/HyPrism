pkgname=hyprism
pkgver=1.0.17
pkgrel=1
pkgdesc="Hytale launcher with mod management and more"
arch=('x86_64' 'aarch64')
url="https://github.com/yyyumeniku/HyPrism"
license=('MIT')
depends=(
  'gtk3'
  'webkit2gtk-4.1'
  'gcc-libs'
)
makedepends=(
  'git'
  'go'
  'nodejs'
  'npm'
  'base-devel'
)
optdepends=(
  'jre21-openjdk:  For running Hytale'
)
provides=("${pkgname}")
conflicts=("${pkgname}-bin" "${pkgname}-git")
source=("${pkgname}-${pkgver}.tar.gz::https://github.com/yyyumeniku/HyPrism/archive/v${pkgver}.tar.gz")
sha256sums=('SKIP')

build() {
  cd "HyPrism-${pkgver}"

  # Set up Go environment
  export GOPATH="${srcdir}/go"
  export PATH="${GOPATH}/bin:${PATH}"

  # Build frontend
  cd frontend
  npm install --frozen-lockfile
  npm run build
  cd ..

  # Install wails to local GOPATH
  go install github.com/wailsapp/wails/v2/cmd/wails@latest

  # Build with wails
  wails build \
    -platform linux/amd64 \
    -ldflags "-X 'app.Version=${pkgver}' -s -w" \
    -o HyPrism
}

package() {
  cd "HyPrism-${pkgver}"

  install -Dm755 "build/bin/HyPrism" "${pkgdir}/usr/bin/hyprism"

  install -Dm644 "LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"

  install -Dm644 "README.md" "${pkgdir}/usr/share/doc/${pkgname}/README.md"

  # Install icon (wails puts it in build/appicon.png, but source has assets/icon.png)
  if [ -f "build/appicon.png" ]; then
    install -Dm644 "build/appicon.png" "${pkgdir}/usr/share/pixmaps/hyprism.png"
  else
    install -Dm644 "assets/icon.png" "${pkgdir}/usr/share/pixmaps/hyprism.png"
  fi

  # Install desktop file if it exists
  if [ -f "hyprism.desktop" ]; then
    install -Dm644 "hyprism.desktop" "${pkgdir}/usr/share/applications/hyprism.desktop"
  fi
}