## Configurer un serveur DHCP sur Debian 10 (Buster)

### Prérequis

- Configuration et/ou interface réseau déjà fonctionnelle
- Accès Root  
Pour l'avoir, taper simplement: `su` suivi du mot de passe du compte root.

### Installation d'ISC DHCP Server:
``apt update``  
``apt install isc-dhcp-server``

Un message d'erreur provenant du serveur DHCP s'affichera juste après la fin de l'installation. 
Le serveur n'a en effet pas pu démarrer, ce qui est tout à fait normal puisqu'il n'est pas encore été configuré.


### Configuration

> [!IMPORTANT]
> Avant d'éditer chaque fichier, nous en ferons une sauvegarde afin de pouvoir retrouver un fichier exploitable en cas de pepin.  
> Pour se faire, nous ferons simplement faire une copie du fichier en rajoutant un .``old`` 

#### Indiquer l'interface à utiliser dans `/etc/default/isc-dhcp-server`
Backup:  
``cp /etc/default/isc-dhcp-server /etc/default/isc-dhcp-server.old``

Éditer le fichier:  
``nano /etc/default/isc-dhcp-server``

Il faudra vers le bas de celui-ci ajouter le nom de l'interface réseau à utiliser.  
Exemple avec enp0s3:
:::image type="content" source="interface.png" alt-text=" ":::

Quitter avec CTRL+X, puis confirmer pour écraser le fichier.

#### Spécifier les options du DHCP dans `/etc/dhcp/dhcpd.conf`
Backup:  
``cp /etc/dhcp/dhcpd.conf /etc/dhcp/dhcpd.conf.old``

Éditer le fichier:  
``nano /etc/dhcp/dhcpd.conf``

Nous allons seulement changer les options qui seront vraiment nécessaires pour que le DHCP puissse tourner:

> [!TIP]
> Pour rappel, un serveur DHCP doit déliver impérativement ces 3 choses au client: 
> - Une adresse IP
> - Un temps de bail
> - Un masque de sous réseau, sans quoi l'adresse IP est inexploitable   

``Définir le DNS distribué par le DHCP``
Vers le haut du fichier il sera possible de définir un nom de domaine et un DNS.
Nous mettrons un domaine en ``quelquechose.local`` et une DNS comme ``1.1.1.1``, celui de CloudFlare.   
Les DNS doivent être séparés par une virgule si l'on souhaite en mettre plusieurs:

:::image type="content" source="DNS.png" alt-text=" ":::

Il est possible de modifier les temps de bail (``default-lease-time`` et ``max-lease-time``). Ce temps est donné en secondes et est de 600 par défaut.

#### Définir l'IP + masque de sous-réseau
Nous allons décommenter (retirer les #) autour de la ligne 30 de sorte à avoir ceci:
:::image type="content" source="uncomment.png" alt-text=" ":::

Nous pouvons ensuite sur cette ligne (la seule en blanche sur l'image) changer l'adresse IP et le masque pour correspondre à notre réseau.

#### Plage d'IP à distribuer
Nous allons ajouter cette option sur une nouvelle ligne entre les crochets. Elle se présente ainsi:  
``range adresse_IP_début adresse_IP_fin;``  
En sachant que les adresses IP début et fin sont distribuées.

> [!CAUTION]
> Attention à ne pas oublier le point virgule ( ; ) en fin de la ligne !

Le résultat devrait ressembler à ceci:
:::image type="content" source="result.png" alt-text=" ":::

Écraser le fichier, et confirmer.

### Est-ce que ça marche ?

Redémarrer le serveur avec:  
``systemctl restart isc-dhcp-server``

Regarder si le serveur est fonctionnel avec:  
``systemctl status isc-dhcp-server``

Décommenter `authoritative`

Subnet
range

Logs /var/log/syslog

Voir un log depuis le bas 

tail -n 25 /var/log/syslog

systemctl start osc-dhcp-server


Indiquer sur quelle carte réseau le DHCP doit écouter
nano /etc/default/isc-dhcp-server

INTERFACESv4="enp0s3"

Vérifier que le DHCP est fonctionnel avec systemctl status