#!/bin/bash

# Disable various shellcheck rules that produce false positives in this file.
# Repository rules should be added to the .shellcheckrc file located in the
# repository root directory, see https://github.com/koalaman/shellcheck/wiki
# and https://archiv8.github.io for further information.
# shellcheck disable=SC2034,SC2154
# [ToDo]: Add files: User documentation
# [ToDo]: Add files: Tooling
# [FixMe]: Namcap warnings and errors

# Maintainer: Ross Clark <https://github.com/orgs/Archiv8/fathom/discussions>
# Contributor: Ross Clark <https://github.com/orgs/Archiv8/fatho/discussionsm>

pkgname="stylelint-stylus"
pkgver=0.16.1
pkgrel=1
pkgdesc="A plugin that allows Stylelint to check Stylus style sheets"
url="https://stylelint.io/"
arch=(
  "any"
)
license=(
  "MIT"
)
depends=(
  # Arch Linux Official Repositories
  "nodejs"
  "stylelint"
)
makedepends=(
  # Arch Linux Official Repositories
  "jq"
  "npm"
)
source=(
  "https://registry.npmjs.org/${pkgname}/-/${pkgname}-${pkgver}.tgz"
)
sha512sums=(
  "d5266804de97cc387d7eb93aa5d5595914d32496644bbccb9e77255f9d7f8d2cf9fa85b45c429bafab49ca2ed02f94c1a7775d9b0e58cd72bed9da668dfeedce"
)

package() {

  # Ensure cache is set when install is run in order to avoid littering user's home directory
  printf "\e[1;32m==>\e[0m \e[38;5;248mBuilding nodejs package... \e[0m\n"
  printf "    This may take some time depending on the size of the package and number of dependencies to download... \e[0m\n"

  npm install --global \
    --cache "${srcdir}/npm-cache" \
    --prefix "${pkgdir}"/usr "$srcdir"/${pkgname}-${pkgver}.tgz

  # Fix incorrect user / group as highlighted by namcap
  printf "\e[1;32m==>\e[0m \e[38;5;248mCorrecting possible ownership issues\e[0m\n"
  while IFS= read -r fsitem; do
    chown -h root:root "$fsitem"
  done < <(find "${pkgdir}" -not -group root -not -user root)

  # Non-deterministic race in npm gives 777 permissions to random directories.
  # See https://github.com/npm/npm/issues/9359 for details.
  printf "\e[1;32m==>\e[0m \e[38;5;248mCorrecting possible permission issues on directories\e[0m\n"
  find "${pkgdir}/usr" -type d -exec chmod 755 '{}' +

  # Remove references to $pkgdir
  printf "\e[1;32m==>\e[0m \e[38;5;248mRemoving references to \${pkgdir}\e[0m\n"
  find "${pkgdir}" -type f -name package.json -print0 | xargs -0 sed -i "/_where/d"

  # Remove references to $srcdir
  printf "\e[1;32m==>\e[0m \e[38;5;248mRemoving references to \$srcdir \e[0m\n"
  local tmppackage="$(mktemp)"
  local pkgjson="${pkgdir}/usr/lib/node_modules/${pkgname}/package.json"
  jq '.|=with_entries(select(.key|test("_.+")|not))' "$pkgjson" > "$tmppackage"
  mv "$tmppackage" "$pkgjson"
  chmod 644 "$pkgjson"
}
