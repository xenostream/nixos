# NixOS for Xenostream

```
sudo -i
parted /dev/sda -- mklabel gpt
parted /dev/sda -- mkpart primary 512MB -8GB
parted /dev/sda -- mkpart primary linux-swap -8GB 100%
parted /dev/sda -- mkpart ESP fat32 1MB 512MB
parted /dev/sda -- set 3 esp on

mkfs.ext4 -L nixos /dev/sda1
mkswap -L swap /dev/sda2
mkfs.fat -F 32 -n boot /dev/sda3

mount /dev/disk/by-label/nixos /mnt
mkdir -p /mnt/boot
mount /dev/disk/by-label/boot /mnt/boot
swapon /dev/sda2
nixos-generate-config --root /mnt

vim /mnt/etc/nixos/configuration.nix
nixos-install

nixos-enter --root /mnt -c 'passwd pjkwon'
reboot


vi /etc/nixos/configuration.nix

networking.hostName = "nixos"; # Define your hostname.
time.timeZone = "Asia/Seoul";
i18n.defaultLocale = "en_US.UTF-8";

services.xserver.enable = true;
services.xserver.displayManager.lightdm.enable = false;
services.xserver.displayManager.startx.enable = true;
services.xserver.windowManager.dwm.enable = true;

nixpkgs.config.allowUnfree = true;
services. xserver.videoDrivers = [ "nvidia" ];
hardware.nvidia = {
  modesetting.enable = true;
  powerManagement.enable = false;
  open = false;
  nvidiaSettings = true;
  package = config.boot.kernelPackages.nvidiaPackages.stable;
};

# boot.kernelParams = [ "nvidia NVreg_PreserveVideoMemoryAllocations=1" ];

users.defaultUserShell = pkgs.zsh;
programs.zsh = {
  enable = true;
  enableCompletion = true;
  autosuggestions.enable = true;
  syntaxHighlighting.enable = true;

  shellAliases = {
    ll = "ls -l";
    update = "sudo nixos-rebuild switch";
  };
};


fonts.packages = with pkgs; [
  nerd-fonts.jetbrains-mono noto-fonts-cjk-sans noto-fonts-cjk-serif noto-fonts-color-emoji
];

i18n.inputMethod = {
  enable = true;
  type = "fcitx5";
  fcitx5.addons = with pkgs; [ fcitx5-hangul ];
};

hardware.bluetooth.enable = true;
services.blueman.enable = true;

environment.systemPackages = with pkgs; [
#   vim # Do not forget to add an editor to edit configuration.nix! The Nano editor is also installed by default.
#   wget
  nextvi dmenu git alacritty

  (pkgs.writeShellScriptBin "st" ''
      exec ${pkgs.alacritty}/bin/alacritty "$@"
  '')
];

users.users.pjkwon = {
  isNormalUser = true;
  extraGroups = [ "wheel" ]; # Enable ‘sudo’ for the user.
   packages = with pkgs; [
     qutebrowser cmus btop bat ntfs3g
   ];
};
----------------------------------------------------------------------------------------------


curl -O https://raw.githubusercontent.com/xenostream/edbrowse/refs/heads/main/config.py.mac
mv .config/qutebrowser/config.py




# Post Install & Configuration

cat .xinitrc
exec amixer set IEC958 unmute &

export XMODIFIERS=@im=fcitx
export QT_IM_MODULE=fcitx
export GTK_IM_MODULE=fcitx

exec xrandr --rate 75 &
exec xset s off &
exec xset -dpms &
exec xset r rate 200 35 &

exec fcitx5 &


exec dwm


# Modify /etc/nixos/configuration.nix
nixos-rebuild switch


# Package MGMT
nix-env -i (install) -e (remove) -qa | grep pkg_name | q (installed list)
nix-collect-garbage


# mount /nix/store RW
mount -o remount,rw /nix/store 


# Bluetooth 
bluetoothctl
power on
agent on
default-agent
scan on
pair XX:YY
connect XX:YY
trust [XX:YY]


# fcitx5
fcitx5-configtool => "Input Method" => "Available Input Method" => Search Input Method => Korea => Hangul => "<"
한글 키 or Ctrl + Space
```

```
Shift + Alt + Q      dwm 종료하기
Shift + Alt +          Enter  터미널 실행하기
Alt + P                    dmenu 열기
Shift + Alt + C      현재 창 닫기

Alt + j, k                창 커서 이동하기
Alt + h, l                마스터 창 크기 조절하기
Alt + d, i                창 마스터/스택으로 이동하기
Alt + Enter            마스터 창과 스택 바꾸기
Alt + 1...9               해당 숫자 태그로 이동하기
Shift + Alt + 1...9  현재 창 해당 태그로 보내기

# 레이아웃
Alt + t                 타일 모드
Alt + f                 플로팅 모드
Alt + m               모노클 모드

## 플로팅 모드
Alt + 우클릭               창 크기 조절하기
Alt + 좌클릭               창 위치 이동하기
Shift + Alt + Space   플로팅 전환하기
```



