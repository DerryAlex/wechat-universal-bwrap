# Maintainer: 7Ji <pugokushin at gmail dot com>
# Maintainer: leaeasy <leaeasy at gmail dot com>
# Maintainer: devome <evinedeng at hotmail dot com>

_pkgname=wechat-universal
pkgname=${_pkgname}-bwrap
pkgver=4.0.0.21
pkgrel=1
pkgdesc="WeChat (Universal) with bwrap sandbox"
arch=('x86_64' 'aarch64' 'loong64')
url="https://weixin.qq.com"
license=('proprietary' 'GPLv3') # GPLv3 as desktop-app was statically linked-in, refer: https://aur.archlinux.org/packages/wechat-universal-bwrap#comment-964013
install="${_pkgname}".install
provides=("${_pkgname}")
conflicts=("${_pkgname}")
replaces=('wechat-beta'{,-bwrap})
depends=(
    'alsa-lib'
    'at-spi2-core'
    'bubblewrap'
    'flatpak-xdg-utils'
    'libxcomposite'
    'libxkbcommon-x11'
    'libxrandr'
    'mesa'
    'nss'
    'pango'
    'xcb-util-image'
    'xcb-util-keysyms'
    'xcb-util-renderutil'
    'xcb-util-wm'
    'xdg-desktop-portal'
    'xdg-user-dirs'
)
if [[ "${CARCH}" == loong64 ]]; then # This is needed instead of a plain declaration because AUR web would return all depends regardless of client's arch, AUR helpers would thus refuse to build the package on non-loong64 due to missing liblol
    depends_loong64=('liblol')
fi
options=(!strip !debug)

source=(
    'fake_dde-file-manager'
    "${_pkgname}.sh"
    "${_pkgname}.desktop"
)

_deb_stem="com.tencent.wechat_${pkgver}"
_deb_url_common="https://home-store-packages.uniontech.com/appstore/pool/appstore/c/com.tencent.wechat/${_deb_stem}"

source_x86_64=("${_deb_url_common}_amd64.deb")
source_aarch64=("${_deb_url_common}_arm64.deb")
source_loong64=("${_deb_url_common}_loongarch64.deb")

noextract=("${_deb_stem}"_{amd,arm,loongarch}64.deb )

sha256sums=(
    'b25598b64964e4a38f8027b9e8b9a412c6c8d438a64f862d1b72550ac8c75164'
    'e3beb121edcb1e6f065226aec9137a7e38fd73af4030ee0e179427162a230fdc'
    '0563472cf2c74710d1fe999d397155f560d3ed817e04fd9c35077ccb648e1880'
)

sha256sums_x86_64=(
    'd6d3bc011b762ee0b03f3eeb3b6e7ff5d4ce21ff51c2a73d4ddaa26534e88d19'
)
sha256sums_aarch64=(
    '5e5f9b4ff597679f2b721bd404031f71da8cc4e211c69e6ac8c444fc5bc40bd2'
)
sha256sums_loong64=(
    '6fa8f7cb5d0739d46f2a84d363d8f68c6a0cfd6f57023748ad035903d75d994c'
)

prepare() {
    declare -A _debian_arch=(
        ['x86_64']='amd64'
        ['aarch64']='arm64'
        ['loong64']='loongarch64'
    )
    echo 'Extracting data.tar from deb...'
    bsdtar -xOf "${_deb_stem}_${_debian_arch[$CARCH]}.deb" ./data.tar.xz |
        xz -cd > data.tar
}

package() {
    echo 'Popupating pkgdir with data from wechat-universal deb file...'
    tar -C "${pkgdir}" --no-same-owner -xf data.tar ./opt/apps/com.tencent.wechat
    mv "${pkgdir}"/opt/{apps/com.tencent.wechat/files,"${_pkgname}"}

    echo 'Installing icons...'
    for res in 16 32 48 64 128 256; do
        install -Dm644 \
            "${pkgdir}/opt/apps/com.tencent.wechat/entries/icons/hicolor/${res}x${res}/apps/com.tencent.wechat.png" \
            "${pkgdir}/usr/share/icons/hicolor/${res}x${res}/apps/${_pkgname}.png"
    done
    rm -rf "${pkgdir}"/opt/apps

    local _wechat_root="${pkgdir}/usr/lib/${_pkgname}"

    echo 'Installing scripts...'
    install -Dm755 wechat-universal.sh "${_wechat_root}"/common.sh
    ln -s common.sh "${_wechat_root}"/start.sh
    ln -s common.sh "${_wechat_root}"/stop.sh
    mkdir -p "${pkgdir}"/usr/bin
    ln -s ../lib/"${_pkgname}"/common.sh "${pkgdir}"/usr/bin/"${_pkgname}"

    echo 'Installing fake deepin file manager...'
    install -Dm755 {fake_,"${_wechat_root}"/usr/bin/}dde-file-manager

    echo 'Installing desktop files...'
    install -Dm644 "${_pkgname}.desktop" "${pkgdir}/usr/share/applications/${_pkgname}.desktop"
}
