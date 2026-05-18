# 1. Przykładowe playbooki Ansible

## Instalacja NGINX

```yaml
---
- name: Instalacja nginx
  hosts: webservers
  become: yes

  tasks:
    - name: Instalacja pakietu nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Uruchomienie i włączenie nginx
      service:
        name: nginx
        state: started
        enabled: yes
```

### Zastosowanie w firmie IT

Taki playbook można wykorzystać do:

* automatycznego wdrażania serwerów WWW,
* przygotowania środowisk developerskich i testowych,
* szybkiego skalowania infrastruktury przy wzroście ruchu,
* standaryzacji konfiguracji serwerów.

Przykład: firma uruchamia nowy portal klienta i musi w kilka minut przygotować 20 identycznych serwerów nginx.

---

## Tworzenie użytkownika

```yaml
---
- name: Tworzenie użytkownika
  hosts: all
  become: yes

  tasks:
    - name: Dodanie użytkownika
      user:
        name: devops
        shell: /bin/bash
        groups: sudo
        append: yes
        state: present

    - name: Dodanie klucza SSH
      authorized_key:
        user: devops
        state: present
        key: "{{ lookup('file', 'id_rsa.pub') }}"
```

### Zastosowanie w firmie IT

Playbook może służyć do:

* onboardingu nowych pracowników,
* nadawania dostępów administratorom,
* centralnego zarządzania kontami,
* wymuszania standardów bezpieczeństwa.

Przykład: nowy administrator trafia do zespołu i automatycznie otrzymuje konto na 50 serwerach wraz z kluczem SSH.

---

## Aktualizacja systemu

```yaml
---
- name: Aktualizacja systemu Linux
  hosts: all
  become: yes

  tasks:
    - name: Aktualizacja pakietów
      apt:
        upgrade: dist
        update_cache: yes

    - name: Usunięcie niepotrzebnych pakietów
      apt:
        autoremove: yes
```

### Zastosowanie w firmie IT

Możliwe zastosowania:

* regularne aktualizacje bezpieczeństwa,
* automatyczne patchowanie serwerów,
* utrzymanie zgodności środowisk,
* redukcja podatności bezpieczeństwa.

Przykład: firma aktualizuje co tydzień kilkaset serwerów bez ręcznego logowania na każdy z nich.

---

# 2. Krótkie opracowanie działania AWX

## Czym jest AWX

AWX to otwartoźródłowa platforma do zarządzania automatyzacją opartą o Ansible. Jest odpowiednikiem wersji community dla Ansible Tower / Red Hat Automation Platform.

AWX udostępnia:

* interfejs WWW,
* REST API,
* harmonogramy zadań,
* zarządzanie użytkownikami,
* logowanie wykonanych operacji,
* integrację z GitLab/GitHub,
* kontrolę uprawnień.

---

## Jak działa AWX

### Główne elementy

| Element     | Funkcja                          |
| ----------- | -------------------------------- |
| Inventory   | Lista serwerów i grup hostów     |
| Project     | Repozytorium z playbookami       |
| Credentials | Dane logowania SSH/API           |
| Template    | Definicja uruchomienia playbooka |
| Job         | Wykonanie zadania                |

---

## Typowy proces działania

1. Administrator dodaje serwery do Inventory.
2. AWX pobiera playbooki z repozytorium Git.
3. Tworzony jest Job Template.
4. Operator uruchamia zadanie ręcznie lub według harmonogramu.
5. AWX wykonuje playbook na wskazanych hostach.
6. Wyniki są zapisywane w logach.

---

## Zalety AWX

* centralne zarządzanie automatyzacją,
* łatwiejsza praca zespołowa,
* historia zmian i audyt,
* ograniczenie błędów ludzkich,
* możliwość delegowania zadań mniej technicznym pracownikom.

---

# 3. Jakie procesy można automatyzować w Intero / CGR.PL

Zakładając profil lokalnej firmy IT / hostingowej / software house z Grudziądza, można automatyzować:

## Infrastruktura IT

* wdrażanie nowych serwerów,
* konfigurację nginx/apache,
* aktualizacje systemów,
* backupy,
* monitoring,
* restart usług po awarii,
* zarządzanie certyfikatami SSL.

---

## Obsługa klientów

* automatyczne zakładanie hostingów,
* generowanie kont FTP i baz danych,
* resetowanie haseł,
* provisioning VPS,
* automatyczne raporty SLA.

---

## DevOps / CI-CD

* deployment aplikacji,
* testy automatyczne,
* rollback aplikacji,
* budowanie środowisk testowych,
* integracja z GitLab CI/Jenkins.

---

## Bezpieczeństwo

* automatyczne aktualizacje bezpieczeństwa,
* skanowanie podatności,
* rotacja kluczy SSH,
* backup konfiguracji,
* automatyczne blokowanie adresów IP.

---

## Administracja wewnętrzna

* onboarding pracowników,
* tworzenie kont i dostępów,
* inwentaryzacja sprzętu,
* automatyczne raporty,
* archiwizacja logów.

---

# Podsumowanie

Ansible i AWX pozwalają ograniczyć ręczną administrację oraz standaryzować działania w firmie IT. W praktyce:

* playbook = instrukcja automatyzacji,
* AWX = centralny system zarządzania playbookami,
* automatyzacja = szybsze wdrożenia, mniej błędów i niższe koszty utrzymania infrastruktury.
