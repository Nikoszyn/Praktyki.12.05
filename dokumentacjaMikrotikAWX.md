# Łączenie rutera Mikrotik z AWX
## 1. Pojęcia
    * host - pojedyńcze urządzenie
    * interface - zbiór urządzeń (hostów)
    * credential - dane do logowania
    * project - łaczy playbooki z AWX
    * template - tworzy autmatyzację łącząc interface z projectem
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
