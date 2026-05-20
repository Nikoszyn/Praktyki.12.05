# Łączenie rutera Mikrotik z AWX

## 1. Pojęcia
* __host__ - pojedyńcze urządzenie
* __interface__ - zbiór urządzeń (hostów)
* __credential__ - dane do logowania
* __project__ - łaczy playbooki z AWX
* __template__ - tworzy autmatyzację łącząc interface z projectem

## 2. Schemat działania
```mermaid
graph LR
A1[Host] -- Credential -->B(Interface)
A2[Host] -- Credential -->B
A3[Host] -- Credential -->B
B --> C((TEMPLATE))
C2[(GITHUB)] --> P([Project])
P --> C
```

## 3. Tworzenie hosta
> Uzupełniamy poszczególne pola
* **Name** - adres ip serwera
* **Desctiption** - nazwa wyświetlana w interfejsie

## 4. Tworzenie invetory
> Uzupełniamy poszczególne pola
* **Name** - nazwa wyświetlana w interfejsie (może być nazwa firmy)
* **Organization** - nazwa organizacji/firmy (można default jeśli nie będzie się nikomu udostępniać AWX; służy bardziej do grupowania użytkowników)
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


> pozostałe playbooki umieszczamy w nadrzędnym folderze  

