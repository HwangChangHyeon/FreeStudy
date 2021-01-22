## 리눅스(CentOS7) + 윈도우(Windows10) 듀얼 부팅 만들기

### 1. 윈도우 설치
- 기본적으로 USB를 꼽고 설치할 때 BIOS Legacy 방식과 BIOS UEFI 방식중 하나를 선택해서 설치할 수 있는데
  빠른 부팅, 종료, 보안이 조금 더 좋은 BIOS UEFI 방식으로 설치하도록 한다. 가장 중요한 것은 듀얼 부팅을 할 때
  윈도우를 UEFI 방식으로 설치하면 리눅스도 UEFI로 설치해야하고 Legacy로 설치하면 Legacy로 설치해야된다.
  그 이유는 Legacy의 경우 파티션이 MBR영역으로 생기는데 윈도우에서 Legacy로 설치하여 파티션을 만들어놓으면 UEFI로
  설치시 MBR영역을 읽어들일 수 없어(UEFI는 GPT영역임) 설치가 불가능해진다. 하여 본인이 원하는 방식의 BIOS 모드로 설치하고
  설치 후 윈도우에서 디스크 관리 -> 본인이 원하는 만큼 볼륨 축소하여 해당 새로운 파티션을 만들어준다.
  
  
### 2. 리눅스 설치
- 이번에는 리눅스 설치 USB를 꼽고 윈도우와 동일한 BIOS모드로 설치 진행을 한다. 리눅스 설치 도중 어떤 OS의 리눅스든
  디스크 선택하는 부분이 나올텐데 새로운 파티션을 만든 크기만큼의 용량이 나오는 곳을 클릭하여 리눅스를 해당 파티션에 설치한다.
  그 뒤로는 그냥 본인이 사용할 리눅스 세팅을 해주면 된다.
  
  
### 3. 듀얼 부팅 설정
- 나는 학교에서 CentOS7을 사용했기 때문에 CentOS7로 듀얼 부팅을 했다. 타 OS의 경우 보통 리눅스를 깔면 GRUB에서 자동으로 Windows가 깔려있는 것을 인식하여
  처음에 컴퓨터를 켰을 때 리눅스OS와 윈도우를 선택할 수 있게 뜨는데 Legacy방식으로 설치시 CentOS만 떠서 Windows로 접근이 불가능했다.
  열심히 구글링하고 모르는 단어를 찾아봐가며 결국 선택 화면을 띄우게 만들 수 있었다.

1. su - root  : 일단 루트로 환경을 바꾼다..
2. vi /etc/default/grub : 기본적인 grub 세팅을 하는 곳이다.
GRUB_TIMEOUT=30  // TimeOut은 처음 켰을 때 선택화면에서 대기 시간을 말하고 기본 5초로 설정되어있어 늘렸다.

3. grub2-mkconfig -o /boot/grub2/grub.cfg : 위에서 grub 세팅 한 것을 적용시키는 명령어이다.

4. vi /boot/grub2/grub.cfg : grub 세팅하는 곳으로 자세한 부분을 설정하는 파일이다.

### BEGIN /etc/grub.d/10_linux ### 
// BEGIN 밑에줄에 아래 6줄을 하나의 철자도 틀리지 않게 똑같이 적는다.

menuentry "Windows10" {
insmod chain
insmod drivemap
set root=(hd0,msdos1)
chainloader +1
}

위 내용과 똑같이 입력 후 저장

4. grub2-set-default “Windows10” : 처음 선택화면에서 리눅스와 윈도우중 기본 선택을 어떤 것으로 할지 정한다.
5. reboot

초기화면이 윈도우로 설정되어있고 30초 대기시간이 걸리면 정상적으로 설치된 것이다.

UEFI 방식으로 설치하는 경우 기본으로 GRUB가 Windows Boot Manager를 잡아준다. 

위 작업을 할 때는 꼭 root 환경에서 해야하니 참고!
