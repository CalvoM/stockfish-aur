# Maintainer: niklas.fiekas at backscattering dot de
# Contributor: mark.blakeney at bullet-systems dot net
# Contributor: Tod Jackson <tod.jackson@gmail.com>
# Contributor: Özgür Sarıer <echo b3pndXJzYXJpZXIxMDExNjAxMTE1QGdtYWlsLmNvbQo= | base64 -d>
# Contributor: user6553591 <Message on Reddit>
# Contributor: P. Badredin <p dot badredin at gmail dot com>
# Contributor: Justin Blanchard <UncombedCoconut at gmail dot com>
# Contributor: Auguste Pop < auguste [at] gmail [dot] com >
# Contributor: SandaruKasa <echo c2FuZGFydWthc2ErYXVyQHlhLnJ1Cg== | base64 -d>
# Contributor: bagasdotme

pkgname=stockfish
pkgver=16.1
pkgrel=1
epoch=1
pkgdesc="A strong UCI chess engine"
arch=('x86_64' 'i686' 'armv7h' 'aarch64')
url="https://stockfishchess.org/"
license=('GPL3')
depends=('glibc')
# Check EvalFileDefaultName{Big,Small} in src/evaluate.h and change accordingly
_net_name_big=('b1a57edbea57')
_net_name_small=('baff1ede1f90')
source=("$pkgname-$pkgver.zip::https://github.com/official-stockfish/Stockfish/archive/sf_$pkgver.zip"
        "https://tests.stockfishchess.org/api/nn/nn-${_net_name_big}.nnue"
        "https://tests.stockfishchess.org/api/nn/nn-${_net_name_small}.nnue")
sha512sums=('bfaa5c644d2acb8538b1a2c72fdf58c6b0ba6cbb3a4e1a335391faa1529f1069eca888295bd4eef0e887860185a3e0e18529ab609829738c420a0c810be5cca4'
            'de2141ba301dd4da0cfa5d3a8f3574fa0ac9b24915fa1802654ad8baf274157feb1fd0ce96aae3893b789a1bb1df8eccdb4a5a331756802bebde3c4d1db5f1de'
            '56358da4810a2bf5b903668d8243ec888a0a64302a893207074b3f1644c9877ddd4557303f025eabcc6e0f7b31af42c1265ba2fd34209df9dd0ea205d4f4a8e9')

prepare() {
  ln -sf "${srcdir}/nn-${_net_name_big}.nnue" "Stockfish-sf_${pkgver}/src"
  ln -sf "${srcdir}/nn-${_net_name_small}.nnue" "Stockfish-sf_${pkgver}/src"
}

build() {
  cd "Stockfish-sf_${pkgver}/src"

  if [[ "$CARCH" == "armv7h" ]]; then
    _arch=armv7
  elif [[ "$CARCH" == "aarch64" ]]; then
    if grep -wq asimddp /proc/cpuinfo; then
      _arch=armv8-dotprod
    else
      _arch=armv8
    fi
  elif [[ "$CARCH" == "i686" ]]; then
    _arch=x86-32
  elif grep -wq avx512dq /proc/cpuinfo && grep -wq avx512vl /proc/cpuinfo && grep -wq avx512_vnni /proc/cpuinfo; then
    # 256 bit operands are faster on most hardware
    _arch=x86-64-vnni256
  elif grep -wq avx512f /proc/cpuinfo && grep -wq avx512bw /proc/cpuinfo; then
    _arch=x86-64-avx512
  elif grep -wq bmi2 /proc/cpuinfo; then
    if grep -wq GenuineIntel /proc/cpuinfo; then
      _arch=x86-64-bmi2
    elif grep -wq AuthenticAMD /proc/cpuinfo && [[ "$(grep --max-count=1 'cpu family' /proc/cpuinfo | sed -e 's/^.*: //')" -ge 25 ]]; then
      _arch=x86-64-bmi2
    else
      # On AMD, bmi2 is emulated before Zen 3, so that using it is a slowdown
      _arch=x86-64-avx2
    fi
  elif grep -wq avx2 /proc/cpuinfo; then
    _arch=x86-64-avx2
  elif grep -wq sse4_1 /proc/cpuinfo && grep -wq popcnt /proc/cpuinfo; then
    _arch=x86-64-sse41-popcnt
  elif grep -wq ssse3 /proc/cpuinfo; then
    _arch=x86-64-ssse3
  elif grep -wq pni /proc/cpuinfo && grep -wq popcnt /proc/cpuinfo; then
    _arch=x86-64-sse3-popcnt
  else
    _arch=x86-64
  fi

  make ARCH="$_arch" profile-build
}

package() {
  cd "Stockfish-sf_${pkgver}/src"
  make PREFIX="$pkgdir/usr" install
}

# vim:set ts=2 sw=2 et:
