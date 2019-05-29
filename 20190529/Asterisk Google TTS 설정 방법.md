# 구글 TTS(Text To Speech) 설정 방법
---

## 구글 TTS를 설치한다.
```
$ yum -y install perl perl-libwww-perl sox cpan
  (perl 관련 패키지 설치)
$ rpm -Uhv http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.3-1.el6.rf.x86_64.rpm X

or

$ rpm -Uvh http://ftp.tu-chemnitz.de/pub/linux/dag/redhat/el6/en/i386/rpmforge/RPMS/rpmforge-release-0.5.3-1.el6.rf.i686.rpm    CentOS 6
$ rpm -Uvh http://ftp.tu-chemnitz.de/pub/linux/dag/redhat/el7/en/x86_64/rpmforge/RPMS/rpmforge-release-0.5.3-1.el7.rf.x86_64.rpm CentOS 7
  (rpmrepo 프로젝트는 RHEL, CentOS 등 다양한 배포판에 맞는 RPM 패키지를 제공하는 오픈 소스 프로젝트이다.)
$ yum -y install mpg123
  (무료이며 오픈 소스 오디오 플레이어이다. mp3를 포함한 mpeg 오디오 포맷을 지원한다.)
$ PERL_MM_USE_DEFAULT=1 perl -MCPAN -e "install Bundle::LWP"
$ PERL_MM_USE_DEFAULT=1 perl -MCPAN -e "install CGI::Util"
$ wget https://github.com/zaf/asterisk-googletts/blob/master/googletts.agi
$ mv googletts.agi /var/lib/asterisk/agi-bin/
$ chown -R asterisk:asterisk googletts.agi
  (chown: 파일 소유권 변경, -R 옵션: 하위 디렉터리에도 모든 권한 변경)
$ chmod 755 /var/lib/asterisk/agi-bin/googletts.agi
  (chmod: 파일 권한 변경, 755: user-rwx,group-rx,other-rx)
$ cd /tmp
$ yum -y install git
  (git 설치)
$ git clone git://github.com/zaf/asterisk-googletts
  (google tts 레파지토리를 로컬에 가져온다.)
$ cd asterisk-goo*
$ cd cli
$ mv googletts-cli.pl /usr/local/sbin/googletts-cli.pl
```
---
## propolys-tts.agi를 구성한다.
```
$ vi /var/lib/asterisk/agi-bin/propolys-tts.agi

>Find
>case 'swift':
> exec($enginebin." -p audio/channels=1,audio/sampling-rate=8000 -o $wavefile -f $textfile");
> break;
>and below add
>case 'googleTTS':
> exec($enginebin." -l de -f $textfile -r 8000 -o $wavefile");
> break;

or 

>case 'googleTTS':
> exec($enginebin." -r ".$format['rate']." -f $textfile -o $tmpwavefile");
> break;
```                        
---
## 편집결과
>case 'swift':
>exec($enginebin." -p audio/channels=1,audio/sampling-rate=8000 -o $wavefile -f $textfile");
>break;
>case 'googleTTS':
>exec($enginebin." -l de -f $textfile -r 8000 -o $wavefile");
>break;
>default:

---
## FreePBX GUI에 음성 엔진 추가 (Settings-Text to Speech Engines)
```
Path: /usr/local/sbin/googletts-cli.pl
Engine: googleTTS
```
---
## 테스트 확인
```
$ googletts-cli.pl -t "test" -f aaa.mp3 -l kr -o 111.wav
$ su asterisk
  (사용자변경)
$ /var/lib/asterisk/sounds
  (음성폴더)
$ siseol
  (음성폴더 -업체)
$ cd /var/www/html
  (폴더링크)
$ ln -s /var/lib/asterisk/sounds/siseol/ voice
  (주소녹음 폴더)
```
```
$ yum -y install rdate
$ rdate -s zero.bora.net
  (rdate: 시간 동기화)
$ yum install lame
$ fwconsole ma upgrade dashboard --edge
$ fwconsole ma updateall
$ fwconsole ma downloadinstall core --tag 13.0.27.7
$ fwconsole ma downloadinstall ivr --tag 13.0.27.7
```

- 참조: https://zaf.github.io/asterisk-googletts/