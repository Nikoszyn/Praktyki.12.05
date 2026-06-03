# Łączenie rutera Mikrotik z AWX

## 1. Pojęcia
* __host__ - pojedyńcze urządzenie
* __interface__ - zbiór urządzeń (hostów)
* __credential__ - dane do logowania
* __project__ - łaczy playbooki z AWX
* __template__ - tworzy autmatyzację łącząc interface z projectem.

## 2. Schemat działania
```mermaid
graph LR
A1[Host] <-- Credential -->B(Interface)
A2[Host] <-- Credential -->B
A3[Host] <-- Credential -->B
B <--> C((TEMPLATE))
C2[(GITHUB)] --> P([Project])
P --> C
```

## 3. Tworzenie hosta
> Klikamy ADD

> Uzupełniamy poszczególne pola
* **Name** - adres ip serwera
* **Desctiption** - nazwa wyświetlana w interfejsie

> Klikamy SAVE
## 4. Tworzenie invetory
> Klikamy ADD

> Uzupełniamy poszczególne pola
* **Name** - nazwa wyświetlana w aplikacji (może to być nazwa firmy)
* **Organization** - nazwa organizacji/firmy (można default, jeśli nie będzie się nikomu udostępniać AWX; służy bardziej do grupowania użytkowników)
* **Variables** - wklejamy poniższy kod:
```
---
ansible_connection: network_cli
ansible_network_os: community.routeros.routeros
ansible_ssh_common_args: '-o StrictHostKeyChecking=no'
```
> Omówienie każdej linijki:
> 1. Określa sposób komunikacji Ansible z Mikrotikiem
> 2. Wskazuje, że system na docelowym urządzeniu to MikroTik Router OS
> 3. Rozwiązuje problem komunikatu o weryfikacji klucza hosta SSH

> Klikamy SAVE
## 5. Stworzenie repozytorium na GitHub
> w nim przechowywujemy wszystkie playbooki
> tworzymy folder o nazwie ```collections```
> tworzymy plik ```requirements.yml```
> wklejamy do niego poniższy kod
```
---
collections:
- name: community.routeros
```
> informuje on o Ansible, że do wykonania operacji potrzebna jest kolekcja ```comunity.routeros```

> pozostałe Playbooki umieszczamy w nadrzędnym folderze.

> tworzymy Playbook, który zainstaluje nam SSH
## 6. Tworzenie Project
> Klikamy ADD

> Uzupełniamy poszczególne pola
* **Name** - nazwa wyświetlana w aplikacji (może to być opis procesu, jaki ma wykonać)
* **Organization** - nazwa organizacji/firmy (można default
* **Source Control Type** - wybieramy GIT
* **Source Control URL** - wklejamy link do repozytorium (np. https://github.com/uzytkownik/repozytorium.git)

> Klikamy SAVE

# AUTOMATYZACJA
> Skrypt tworzy 3 użytkowników, każdy z nich ma swoje uprawnienia (full, read, write). Nie mogą się oni logować przez WinBox. Każdy z nich ma ten sam klucz publiczny.

> tworzymy playbooka o nazwie ```tworzenieKont.yml```
```
---
- name: Konfiguracja uzytkownikow i kluczy SSH na MikroTik
  gather_facts: false
  hosts: all
  connection: network_cli

  tasks:
    - name: Przygotowanie pliku klucza i stworzenie 3 nowych grup
      community.routeros.command:
        commands:
          - /file remove [find name="ansible_key.pub.txt"]
          - :if ([:len [/user group find name="ssh_full"]] = 0) do={ /user group add name="ssh_full" policy="local,ssh,read,write,policy,test,password,sniff,romon,sensitive" comment="Dostep WYLACZNIE przez SSH" }
          - :if ([:len [/user group find name="ssh_read"]] = 0) do={ /user group add name="ssh_read" policy="local,ssh,read,test" comment="Dostep WYLACZNIE przez SSH" }
          - :if ([:len [/user group find name="ssh_write"]] = 0) do={ /user group add name="ssh_write" policy="local,ssh,read,write,test" comment="Dostep WYLACZNIE przez SSH" }
      failed_when: false # Ignorujemy błąd, jeśli plik do usunięcia nie istniał

    - name: Odczekanie chwili na wygenerowanie pliku
      ansible.builtin.pause:
        seconds: 2

    - name: Bezpieczne dodanie uzytkownikow (tylko jesli nie istnieja)
      community.routeros.command:
        commands:
          - :if ([:len [/user find name="user_full"]] = 0) do={ /user add name="user_full" group="ssh_full" comment="Konto FULL" password="LosoweSkomplikowaneHaslo123!" }
          - :if ([:len [/user find name="user_write"]] = 0) do={ /user add name="user_write" group="ssh_write" comment="Konto WRITE" password="LosoweSkomplikowaneHaslo123!" }
          - :if ([:len [/user find name="user_read"]] = 0) do={ /user add name="user_read" group="ssh_read" comment="Konto READ" password="LosoweSkomplikowaneHaslo123!" }

    - name: Import kluczy SSH dla uzytkownikow
      community.routeros.command:
        commands:
          - /file print file=ansible_key.pub
          - /file set ansible_key.pub.txt contents="{{ ssh_public_key_string }}"
          - /user ssh-keys import user="user_full" public-key-file="ansible_key.pub.txt"
          - /file print file=ansible_key.pub
          - /file set ansible_key.pub.txt contents="{{ ssh_public_key_string }}"
          - /user ssh-keys import user="user_write" public-key-file="ansible_key.pub.txt"
          - /file print file=ansible_key.pub
          - /file set ansible_key.pub.txt contents="{{ ssh_public_key_string }}"
          - /user ssh-keys import user="user_read" public-key-file="ansible_key.pub.txt"
```
## 7. Tworznenie klucza SSH
> Aby utworzyć klucz ssh, włączamy CMD

> Przechodzimy do naszego katalogu np.```cd ./Desktop```
>
> Wpisujemy następujące polecenie
```
ssh-keygen -t ed25519 -C NAZWA_FIRMY
```
> Utworzy ono 2 pliki:
* **ed25519** - jest to klucz prywatny
* **ed25519.pub** - jest to klucz publiczny
> Możemy zmienić nazwę pilków na związane z firmą wyrażenie

## 8. Tworzenie Credentials
> Stworzymy od razu 2 różne wersje logowania (przez hasło oraz przez Klucz SSH)
>
> Klikamy ADD
### Przez Hasło
> Uzupełniamy poszczególne pola
* **Name** - nazwa wyświetlana w aplikacji (może to być nazwa firmy)
* **Credential Type** - wybrać ```Machine```
* **Nazwa Użytkownika** - nazwa użytkownika w ruterze (przeważnie admin)
* **Hasło** - hasło użytkownika.

### Przez Klucz
> Uzupełniamy poszczególne pola
* **Name** - nazwa wyświetlana w aplikacji (może to być nazwa firmy)
* **Credential Type** - wybrać ```Machine```
* **Nazwa Użytkownika** - nazwa użytkownika w ruterze (przeważnie admin)
* **Signed SSH Certificate** - przeciągnąć albo wkleić prywatny klucz SSH

## 9. Połącznie wszystkiego w Template
> Klikamy ADD i uzupełniamy poszczególne pola
* **Name** - nazwa opisująca Zdarzenie (np. nazwa firmy + opis procesu)
* **Job Type** - wybieramy run
* **Inventory** - wybieramy Nasze Inventory
* **Project** - wybieramy Nasz Project
* **Playbook** - Wybieramy playbook ```tworzenieKont.yml```
* **Credentials** - wybieramy Nasze Crednetial (logujący się przez hasło)
> Klikamy SAVE
## 10. Uruchomienie
> Wybieramy nasz Template i klikamy Launch.

## 11. Po instalacji
> Możemy usunąć Credential z logowaniem przez hasło
> Podczas następnych użyć możemy zmieniać Playbooki.

## *. Konfiguracja środowiska wykonywalnego
> Tworzymy konto na docker i pobieramy docker desktop

> Na pulpicie tworzymy folder o nazwie EEcustom, a w nim plik ```Dockerfile``` o zawartości
```
FROM quay.io/ansible/awx-ee:latest
USER root
RUN pip3 install ansible-pylibssh
USER 1000
``` 
> następnie w cmd w tym katalogu wykounjemy poniższe polecenia
```
docker build -t custom-ee:v1.0 .
```
> Tutaj będziemy musieli się zalogować 
```
docker login
```
```
docker tag custom-ee:v1.0 NAZWAPROFILUNADOCKER/custom-ee:v1.0
```
```
docker push NAZWAPROFILUNADOCKER/custom-ee:v1.0
```
> W AWX przechodimy do zakładki Execution Environment, klikamy ADD
* **Name** - nazwa EE
* **Image** - należy wpisać ```docker.io/NAZWAPROFILUNADOCKER/custom-ee:v1.0```
# Gotowe.
