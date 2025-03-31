# configuration-pfSense-Proxmox
configuration-de-VLANs-avec-pfSense-dans-Proxmox
# Configuration de VLANs avec pfSense dans Proxmox

## Sommaire
- Licence
- Description du projet
- Outils du lab
- Étape par étape

## Licence :
Cette documentation est sous licence CC BY 4.0

## Description du projet :
Cette procédure a pour objet de simuler le comportement d'un commutateur réseau configuré avec le standard IEEE 802.1Q dans l'environnement virtualisé qu'offre Proxmox.

## Outils du lab :
- Proxmox (8.2.7)
- pfSense (2.7.2)
- Open vSwitch (3.1.0)
```sh
apt install openvswitch-switch 
```

### 2 - (sur le node) Dans le menu **System/Network**, créer l'interface VLAN (un `vmbr` qui fera office de switch) :
- Appuyer sur le bouton **Create**
- Choisir **OVS Bridge**
- Nommer le bridge et éventuellement le commenter
- Appuyer sur **Create**
![image](https://github.com/user-attachments/assets/e643ba6b-a80f-47f9-930a-2bb4b2b7309f)

### 3 - (sur le node) Dans le menu **System/Network**, créer les VLANs (ce qui permettra de "taguer" les trames des machines) :
- Appuyer sur **Create**
- Choisir **OVS InPort**
- Nommer par le nom du VLAN (exemple : vlan100)
- Choisir l'OVS Bridge correspondant (celui créé à l'étape 1)
- Préciser le **VLAN Tag**
- Appuyer sur **Create**
- Répéter pour tous les VLANs à créer
- Appuyer sur le bouton **Apply Configuration**
![image](https://github.com/user-attachments/assets/9a8eb2c7-d064-41ef-b002-144c7ad615cc)

### 4 - (sous le node) Configurer la VM pfSense :
- Cliquer sur la **VM pfSense**
- Cliquer sur **Hardware**
- Cliquer sur le bouton **Add**
- Cliquer sur **Network Device** pour ajouter la 3ème interface
- Choisir l'OVS Bridge qui a été créé pour faire office de switch  
  *(ce comportement simulera le fait de connecter physiquement la 3ème carte réseau de la VM pfSense au switch dans lequel on aurait configuré les VLANs)*
![image](https://github.com/user-attachments/assets/575367a5-ca47-4a59-88e5-0f54ef5584d6)

### 5 - Configurer pfSense :
- Dans **Interfaces/Assignments/VLANs** :
  - Cliquer sur le bouton **+ Add**
  - Dans **Parent Interfaces**, choisir l'interface (OVS Bridge faisant office de switch L2)
  - Préciser le **VLAN Tag**
  - Préciser une description si souhaité
- Retourner dans **Interface Assignments** :
  - Cliquer sur le bouton **+ Add**
  - Cliquer sur le VLAN avec le bon TAG
  - Cliquer sur le nom de l'interface **OPTx** :
    - Activer l'interface
    - Lui donner un nom évocateur (qui apparaîtra dans le shell)
    - Préciser le type de configuration IP
    - Configurer l'adresse de l'interface virtuelle du VLAN
    - Préciser le masque
    - Appuyer sur le bouton **Save**
    - Appuyer sur **Apply Changes**

### 6 - Configurer les règles de flux par interface VLAN :
- Dans **Firewall/Rules** :
  - Choisir les interfaces sur lesquelles créer des règles
  - Configurer le pare-feu en fonction de votre politique de sécurité

### 7 - (sous le node) Faire correspondre les VMs à intégrer dans les VLANs à leur VLAN correspondant :
- Cliquer sur la **VM**
- Cliquer sur **Hardware**
- Cliquer sur le bouton **Add**
- Cliquer sur **Network Device** pour ajouter leur interface réseau
- Choisir l'OVS Bridge qui a été créé pour faire office de switch
- Préciser le **VLAN Tag** dans lequel il faut intégrer la machine
- Cliquer sur le bouton **+ Add**
![image](https://github.com/user-attachments/assets/894b6c12-bc6c-4a9d-914c-9cc270fe2d67)
