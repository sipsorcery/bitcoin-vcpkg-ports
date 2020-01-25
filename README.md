VCPKG_INSTALL_PATH: 'C:\tools\vcpkg\installed'
VCPKG_PORTS_REPO: 'https://github.com/sipsorcery/bitcoin-vcpkg-ports.git'
VCPKG_PORTS_DIR: 'c:\tools\vcpkg-ports'

$env:PACKAGES = Get-Content -Path build_msvc\vcpkg-packages.txt
Write-Host "vcpkg list: $env:PACKAGES"
$env:GIT_REDIRECT_STDERR = '2>&1' # git is writing non-errors to STDERR when doing git clone. Send to STDOUT instead.
git clone $env:VCPKG_PORTS_REPO $env:VCPKG_PORTS_DIR
Add-Content "C:\tools\vcpkg\triplets\$env:PLATFORM-windows-static.cmake" "set(VCPKG_BUILD_TYPE release)"
vcpkg install --triplet $env:PLATFORM-windows-static --overlay-ports=$env:VCPKG_PORTS_DIR $env:PACKAGES.split() > $null
# Deliberately running the same command again to print out confirmation that all packages are installed.
vcpkg install --triplet $env:PLATFORM-windows-static --overlay-ports=$env:VCPKG_PORTS_DIR $env:PACKAGES.split()
vcpkg integrate install
