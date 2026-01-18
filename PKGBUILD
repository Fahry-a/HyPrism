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

  cd frontend
  npm install --frozen-lockfile
  npm run build
  cd ..

  go install github.com/wailsapp/wails/v2/cmd/wails@latest

  ~/.local/share/go/bin/wails build \
    -platform linux/amd64 \
    -ldflags "-X 'app.Version=${pkgver}' -s -w" \
    -o HyPrism
}

package() {
  cd "HyPrism-${pkgver}"

  install -Dm755 "build/bin/HyPrism" "${pkgdir}/usr/bin/hyprism"

  install -Dm644 "LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"

  install -Dm644 "README.md" "${pkgdir}/usr/share/doc/${pkgname}/README.md"

  install -Dm644 "assets/icon.png" "${pkgdir}/usr/share/pixmaps/hyprism.png"

  install -Dm644 "hyprism.desktop" "${pkgdir}/usr/share/applications/hyprism.desktop"
}