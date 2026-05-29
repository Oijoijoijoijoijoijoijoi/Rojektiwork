# Asennusdokumentaatiot
### Keskeiset komponentit:
- Arch linux läppärille
- Docker + docker compose, 2 kontin palvelukokonaisuus

## Linuxin asennus (Arch linux)
Asensin Archin kannettavan tietokoneen SSD:lle.

1. Asennusmedian valmistelu usb-tikulle:
    
    1. [Archin ISO-kuva ladattu](https://archlinux.org/download/#download-mirrors)
    2. usb-tikku pöytäkoneeseen (jossa ubuntu)
    3. terminaliin komento  `lsblk` -> usb tikku `/dev/sda1`
    4. mountataan tikku pois ISOn kopioinnin ajaksi `sudo umount /dev/sda1`
    5. `sudo dd if=/home/salakala/Downloads/archlinux-x86_64.iso of=/dev/sda bs=4M status=progress oflag=sync` dd työkalulla kopioidaan ISO-kuva usb-tikulle
    6. dd -operaation jälkeen `sudo partprobe /dev/sda` -> tikku ilmestyy näkyviin graafiseen käyttöliittymään. Avataan tikun sisältö ja varmistetaan visuaalisesti, että ISO-kuva on onnistuneesti tikulla
    7. `sudo umount /dev/sda1` ja tikku pois usb-portista
    8. valmistelu on... Valmis.

2. Kannettavan valmistelu Arch linuxin asennusta varten:

    1. usb-tikku kannettavan usb-porttiin
    2. kone käyntiin ja maanista F painikkeiden rämppäämistä, jotta pääsee BIOSIIN
    3. BIOSista secure boot poist päältä ja boottausjärjestyksessä nostetaan usb-tikku ensimmäiseksi
    4. uudelleenkäynnistys

3. Arch linux asentaminen SSD:lle kannettavaan, usb-tikulla olevan ISO-kuvan avulla

    1. läppäri käynnistyi ja boottasi alkunäkymään, jossa valitsin `Arch Linux install medium (X86_64, UEFI)`
    1. tämän jälkeen varsinainen väliaikainen usb-tikulta käynnistetty Arch lähti käyntiin ja tipautti terminaliin root-käyttäjänä
    2. yhdistin langattomaan verkkoon seuraavanlaisesti
        1. terminaliin `iwctl` -> pääsee uuteen terminaliin, jolla voi yhdistää laitteen langattomaan verkkoon
        2. `[iwd]# device list` -> verkkokortti tunnistettu nimellä wlan0 
        3. `[iwd]# station wlan0 scan` -> skannataan langattomat verkot. Ei näytä vielä mitään.
        4. `[iwd]# station wlan0 get-networks` -> näyttää tarjolla olevat langattoman verkot
        5. `[iwd]# station wlan0 connect omaSSID1234` -> yhdistää langatttomaan verkkoon. Pyytää salasanan tarvittaessa.
        6. Onnistuneen yhdistämisen jälkeen taisin päästä alkuperäiseen terminaliin kirjoittamalla `exit` 
    3. Muistaakseni tämän jälkeen Arch latasi joitain päivityksiä, joten piti etsiskellä, että mikä oli alussa vilahtaneen 'convenience' skriptin nimi.
    4. komento oli `archinstall` -> interaktiivinen asennus/kongifuraatiovalikko aukesi, jossa pystyi säätämään laajasti erilaisia asetuksia, joilla Arch lopulta asentui kannettavan SSD:lle. Tästä vaiheesta listaan vain muutaman relevanteimman, koska muuten tästä tulee pitkä sepustus...
        >  (Halusin kokeilla pystyykö tämmöinen convenience skripti hoitamaan asennuksen kädestä pitäen, koska joskus tuli asennettua openwrt eräälle raudalle ihan tahallaan vaikeimman kautta, usb-ttl adapterilla, mtk_uartboot työkalun kautta ja asennusvaiheet olivat pitkälti samanlaisia, kuin Archin asentamisessa ilman convenience skriptiä)
        1. `archinstall` skriptin valikossa etenkin SSD-levyn säätö asennusta varten kiinnosti, koska SSD:llä oli jo valmiiksi ubuntun  efi  ja / partitiot.
            1. navigoin archinstallin päänäkymässä kohtaan `Disk configuration `- > `partitioning`
            2. `Select a disk configuration` -> `Use a best-effort default partition layout`
            3. `Select disks for the installation` -> valitsin SSD:n, jossa tosiaan oli jo Ubuntu asennettuna
            4. tässä kohtaa yllätyksekseni näkyviin pätkähti joku wizard installer configuration, joka väitti pystyvänsä hoitamaan SSD:n säätämisen itsekseen siihen kuntoon, että olemassaoleva Ubuntu-järjestelmä ei olisi ongelma (eli lanaa koko SSD:n tyhjäksi)
            5. valitsin tämän vaihtoehdon ja vielä tiedostojärjestelmäksi `ext4` -> wizard installeri mietti hetken ja totesi, että kaikki ok.
        2. asetin root-käyttäjälle salasanan ja loin myös erillisen käyttäjän omalla salasanallaan
        3. säädin myös muuta pientä, näppäimistöa sun muuta, ei niin releventtia ja kaikki oli helpohkoa tällä archinstall -skriptillä
        4. `profile` kohdasta jätin tarkoituksellani profiiliksi `minimal`, koska en ollut aiemmin asentanut terminalissa pyörivään järjestelmään graafista käyttöliittymää "päälle". Eli SSD:lle asennuksen jälkeen Archi käynnistyi "vain" terminaliin.
        5. kun olin  tyytyväinen valittuihin konfiguraatioihin, valitsin valikosta `install`, jonka jälkeen järjestelmä alkoi asentumaan SSD:lle.
        6. kaikeksi yllätyksekseni järjestelmä asentui mutkitta SSD:lle, vaikka käytinkin automaagista wizard installeria 
        7. asennuksen päätteeksi näkyviin tuli vielä vaihtoehtoja, joista valitsin `Reboot system` 
        8. nykäisin usb-tikun pois salamannopeasti läppärin bootatessa ja kone käynnistyi SSD:n kautta Archin terminaliin. Success.

> Asensin myös muutaman työpöytäympäristön onnistuneesti (KDE Plasma + XFCE4), mutta näiden asennusvaiheista en käy sen enempää läpi, koska alkoi ärsyttämään läpppärin pieni näyttö niin pahasti, että seuraavien osioiden asennukset ja kaikki muutkin on tehty SSH:n + tmuxin kautta pöytäkoneeltani, jossa on kaksi näyttöä tarjolla...

## Valittu palvelu/järjestelmäkokonaisuus

Kokeilin asentaa Dockerin ja pyörittää 2 docker konttia, jotka "juttelevat" keskenään
ja toinen kontti myös juttelee (kuuntelee) usb-portin kanssa. Kutakuinkin tämmöisellä idealla:
`esp32 -usb-> Arch läppäri -docker-> micro ros agent -docker bridge-> ROS2 `

Kontti 1, microros agent:
 - > Vastaanottaa esp32 minimaaliset (XRCE-DDS) ROS viestit -> muuntaa täysiksi (DDS) ROS viesteiksi -> välittää eteenpäin ROS2 kontille

Kontti 2, ROS2 (jazzy):
- > Vastaanottaa täydet ROS viestit, tässä tapauksessa esp32 lähettämät /joint_states, joilla voidaan esimerkiksi visualisoida fyysisiä robottikäsiä digital twin ympäristössä

Eli yhdistän esp32:n usb-johdolla läppäriin. Esp32 sisällä on microros middleware ja ohjelmakoodi, jolla se julkaisee nuita ROS XRCE-DDS viestejä 20ms välein (leader/joint_states ja follower/joint_states). Jos kaikki menee hyvin, niin molempien konttien terminalissa pitäisi saada komennoilla näkyviin diagnostiikkaa, joilla voi vahvistaa, että onko järjestelmän käyttöönotto onnistunut.

## Palvelun/järjestelmäkokonaisuuden konffaus + käyttöönotto Archille

### Asennus/konfigurointi:

1. Dockerin + docker-composen asennus ja minimaaliset konffaukset
    1. `sudo pacman -S docker` asentaa Dockerin 
    2. `sudo systemctl enable docker.service` käynnistyy automaagisesti läppärin käynnistyessä
    3. `sudo usermod -aG docker $USER` ei tarvitse näpytellä sudoa jokaisen docker komennon eteen. Piti kirjautua uudelleen tämän jälkeen.
    4. `sudo pacman -S docker-compose` docker-compose helpottaa useammasta kontista koostuvan kokonaisuuden hallinnointia

2. luodaan kansio projektille `mkdir konttikopla` ja hypätään kansioon `cd konttikopla` 

3. Konttien #1 ja #2 konffaus + docker-compose.yml
    <details>
    <summary>Kontti #1, luodaan Dockerfile.microros-agent</summary>

    `nano Dockerfile.microros-agent` johon työnnetään allaoleva:

    ```dockerfile
    FROM microros/micro-ros-agent:jazzy

    USER root
    RUN apt-get update && apt-get install -y --no-install-recommends \
        minicom \
        screen \
        python3-pip \
        python3-setuptools \
        && pip3 install --upgrade --ignore-installed esptool --break-system-packages \
        && rm -rf /var/lib/apt/lists

    WORKDIR /uros_ws
    ```

    </details>
    
    <details>
    <summary>Kontti #2, luodaan Dockerfile.ROS2</summary>

    `nano Dockerfile.ROS2` johon työnnetään allaoleva:

    ```dockerfile
    FROM ros:jazzy-ros-base

    ARG UNAME=user
    ARG UID=1000
    ARG GID=1000

    RUN groupadd -g ${GID} -o ${UNAME} \
        && useradd -m -u ${UID} -g ${GID} -o -s /bin/bash ${UNAME} \
        && apt-get update && apt-get install -y sudo \
        && echo "${UNAME} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

    RUN apt-get update && apt-get install -y --no-install-recommends \
        ros-jazzy-foxglove-bridge \
        ros-jazzy-robot-state-publisher \
        ros-jazzy-joint-state-publisher-gui \
        ros-jazzy-tf2-ros \
        ros-jazzy-xacro \
        python3-colcon-common-extensions \
        build-essential \
        && rm -rf /var/lib/apt/lists/*

    USER ${UNAME}
    WORKDIR /home/${UNAME}/so101_ws

    RUN echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc \
        && echo "if [ -f /home/${UNAME}/so101_ws/install/setup.bash ]; then source /home/${UNAME}/so101_ws/install/setup.bash; fi" >> ~/.bashrc

    CMD ["/bin/bash"]
    ``` 
    </details>

    <details>
    <summary>docker-compose.yml</summary>

    `nano docker-compose.yml` johon työnnetään allaoleva:

    ```dockerfile
    services:
      microros-agent:
        build:
          context: .
          dockerfile: Dockerfile.microros-agent
        container_name: microros-agent
        network_mode: host
        privileged: true
        environment:
          - ROS_DOMAIN_ID=100
        volumes:
          - /dev:/dev
          - .:/scripts
        restart: unless-stopped

        command: serial --dev /dev/ttyUSB0 -b 921600 -v6

    
      ros2:
        build:
          context: .
          dockerfile: Dockerfile.ROS2
        container_name: ros2
        network_mode: host
        environment:
          - ROS_DOMAIN_ID=100
        restart: unless-stopped
        stdin_open: true 
        tty: true
        command: /bin/bash
    ``` 
    </details>

    
docker-compose.yml "liimaa" Dockerfilet keskenään samaan pakettiin. Tosin usb-portti on kovakoodattu compose.yml tiedostoon, mikä on aika derp, koska nyt homma toimii, vain jos esp32 on laitettu ensimmäisenä usb-laitteena läppäriin.

### Konttien käyttöönotto

1. käyttöönotetaan ajamalla komento `docker compose up` kansiosta konttikopla ja rukoillaan että kaikki menee hyvin
    1. side quest, keskeytetään ja asennetaan docker Buildx `sudo pacman -S docker-buildx` 
    2. jonka jälkeen ajetaan uudelleen `docker compose up`

2. docker konttien käyttöönotto onnistui, `docker ps` näyttää seuraavaa
    ```
    CONTAINER ID   IMAGE                        COMMAND                  CREATED          STATUS          PORTS     NAMES
    f601c45ec743   konttikopla-ros2             "/ros_entrypoint.sh …"   35 seconds ago   Up 34 seconds             ros2
    e6f584e5e8c4   konttikopla-microros-agent   "/bin/sh /micro-ros_…"   35 seconds ago   Up 34 seconds             microros-agent
    ```


### Konttien  testaus

1. microros-agent kontin testaus:

    
    1. katsotaan logit  `docker logs microros-agent`
    ```
    [1780042754.089412] info     | TermiosAgentLinux.cpp | init                     | Serial port not found. | device: /dev/ttyUSB0, error 2, waiting for connection...
    [1780042755.095258] info     | TermiosAgentLinux.cpp | init                     | Serial port not found. | device: /dev/ttyUSB0, error 2, waiting for connection...
    [1780042756.103714] info     | TermiosAgentLinux.cpp | init                     | Serial port not found. | device: /dev/ttyUSB0, error 2, waiting for connection...
    ```

    > Hiljaista on. Miksi? Koska ESP32 ei ole vielä kiinni läppärissä...
    
    2. työnnetään esp32 Arch läppäriin kovakoodattua ttyUSB0 logiikka noudattaen siten, että ei muita usb-laitteita läppärissä


    3. tsekataan logit uudelleen `docker logs --tail 20 microros-agent`, otetaan vain 10, koska esp32 -> läppäri välillä vilisee nyt miltei sata viestiä sekunnissa joten `docker logs microros-agent` ei ole hyvä vaihtoehto
    ```
    [1780043365.643014] debug    | SerialAgentLinux.cpp | send_message             | [** <<SER>> **]        | client_key: 0xBA5EBA11, len: 13, data: 
    0000: 81 00 00 00 0A 01 05 00 65 59 00 00 80
    [1780043365.644992] debug    | SerialAgentLinux.cpp | recv_message             | [==>> SER <<==]        | client_key: 0xBA5EBA11, len: 13, data: 
    0000: 81 00 00 00 0B 01 05 00 64 59 64 59 80
    [1780043365.645145] debug    | SerialAgentLinux.cpp | send_message             | [** <<SER>> **]        | client_key: 0xBA5EBA11, len: 13, data: 
    0000: 81 00 00 00 0A 01 05 00 65 59 00 00 80
    [1780043365.653869] debug    | SerialAgentLinux.cpp | recv_message             | [==>> SER <<==]        | client_key: 0xBA5EBA11, len: 204, data: 
    0000: 81 80 65 59 07 01 C4 00 59 6F 00 05 65 4E 19 6A 14 4B 12 27 0C 00 00 00 6C 65 61 64 65 72 5F 62
    0020: 61 73 65 00 06 00 00 00 0D 00 00 00 73 68 6F 75 6C 64 65 72 5F 70 61 6E 00 65 6E 73 0E 00 00 00
    0040: 73 68 6F 75 6C 64 65 72 5F 6C 69 66 74 00 3A 3A 0B 00 00 00 65 6C 62 6F 77 5F 66 6C 65 78 00 00
    0060: 0B 00 00 00 77 72 69 73 74 5F 66 6C 65 78 00 00 0B 00 00 00 77 72 69 73 74 5F 72 6F 6C 6C 00 00
    0080: 08 00 00 00 74 72 69 67 67 65 72 00 06 00 00 00 00 00 00 00 F3 71 A0 72 9E 7E E8 BF BA 86 21 32
    00A0: 2D 78 E4 BF 38 51 06 BB 0F F9 D6 BF 75 A7 BB E9 BB FD 85 3F 55 83 F9 82 A7 26 D8 3F 58 75 C8 21
    00C0: 24 D0 E4 3F 00 00 00 00 00 00 00 00
    [1780043365.653999] debug    | DataWriter.cpp     | write                    | [** <<DDS>> **]        | client_key: 0x00000000, len: 192, data: 
    0000: 65 4E 19 6A 14 4B 12 27 0C 00 00 00 6C 65 61 64 65 72 5F 62 61 73 65 00 06 00 00 00 0D 00 00 00
    0020: 73 68 6F 75 6C 64 65 72 5F 70 61 6E 00 65 6E 73 0E 00 00 00 73 68 6F 75 6C 64 65 72 5F 6C 69 66
    0040: 74 00 3A 3A 0B 00 00 00 65 6C 62 6F 77 5F 66 6C 65 78 00 00 0B 00 00 00 77 72 69 73 74 5F 66 6C
    0060: 65 78 00 00 0B 00 00 00 77 72 69 73 74 5F 72 6F 6C 6C 00 00 08 00 00 00 74 72 69 67 67 65 72 00
    0080: 06 00 00 00 00 00 00 00 F3 71 A0 72 9E 7E E8 BF BA 86 21 32 2D 78 E4 BF 38 51 06 BB 0F F9 D6 BF
    00A0: 75 A7 BB E9 BB FD 85 3F 55 83 F9 82 A7 26 D8 3F 58 75 C8 21 24 D0 E4 3F 00 00 00 00 00 00 00 00


    ```

    > esp32 lähettää leader/joint_states ja follower/joint_states viestejä, jotka siis simuloivat kahden robottikäden servojen sijanteja. Jos kaikki menee hyvin, nämä viestit pitäisi olla myös välitetty eteenpäin ROS2 konttiin.
 
 2. ros2 kontin testaus:
 
    1. katsotaan ros2 kontin topicit, onko konttien välillä eloa `docker exec -it ros2 /ros_entrypoint.sh ros2 topic list`
    ```
    /follower/joint_states
    /leader/joint_states
    /parameter_events
    /rosout
    ```

    2. katsotaan millä aikavälillä viestejä tulee, jos tulee `docker exec -it ros2 /ros_entrypoint.sh ros2 topic hz /leader/joint_states`
    ```
    average rate: 49.826
	min: 0.016s max: 0.023s std dev: 0.00244s window: 51
    average rate: 49.940
	min: 0.014s max: 0.024s std dev: 0.00252s window: 102
    average rate: 49.991
	min: 0.014s max: 0.024s std dev: 0.00239s window: 153

    ```
    > Success ~50hz = ~20ms! esp32 -> Arch läppäri -> microros-agent kontti -> ROS2 kontti kokonaisuus toimii! Tähän kun hakee .urdf -tiedostot sun muut, niin fyysistä roboa/roboja pystyy visualisoimaan tietokoneella digital twininä!


## Lopputuumit

Toteutin siis eräällä toisella kurssilla ylläolevan esp32 + docker konttien kokonaisuuden (+ESP-IDF kehitysympäristö development container kontissa, mutta sitä en nyt ottanut tähän mukaan), pyörittäen koko höskää Ubuntulta käsin. Halusin kokeilla, että millaisella säädöllä saman kokonaisuuden saa Archille pyörimään ja yllätyin, miten vähän piti säätää yhtään mitään. Archi vaikuttaa ihan kivalta, taidanpa jättää sen läppärilleni. 
Eniten meni aikaa, kun yritin säätää wifiä kuntoon (en saanut kuntoon), mutta siitä nyt ei olisi tullut mitään kovin mielenkiintoista dokumentaatiota.



### Joitakin käytettyjä lähteitä:
https://wiki.archlinux.org/title/Main_page

https://bbs.archlinux.org/

https://docs.docker.com/

https://www.markdownguide.org/basic-syntax/

lisäksi tekoäly (Gemini) ja hakukoneet
