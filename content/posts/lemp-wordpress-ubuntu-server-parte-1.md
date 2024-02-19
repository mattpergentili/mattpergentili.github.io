+++
title = "Linux - LEMP Stack + WordPress su Ubuntu Server 22.04 LTS - Parte 1"
description = "Guida pratica allo stack lemp e wordpress su ubuntu server 22.04 LTS"
date = "2024-02-19T14:28:54+01:00"
tags = ["", ""]
categories = ["", ""]
draft = true
+++

## Installare il LEMP stack su Ubuntu 22.04

Lo stack software LEMP è un gruppo di software che può essere utilizzato per servire pagine web dinamiche e applicazioni web scritte in PHP. Si tratta di un acronimo che descrive un sistema operativo Linux, con un web server Nginx (pronunciato come "Engine-X"). I dati del backend sono memorizzati nel database MySQL e l'elaborazione dinamica è gestita da PHP.

Questa guida mostra come installare uno stack LEMP su un server Ubuntu 22.04. Il sistema operativo Ubuntu si occupa della parte Linux dello stack. Descriveremo come far funzionare il resto dei componenti.

Prerequisiti

Per completare questo tutorial, è necessario disporre di accesso a un server Ubuntu 22.04 come utente sudo regolare non root e un firewall abilitato sul server. Per configurarlo, è possibile seguire la nostra guida di configurazione iniziale del server per Ubuntu 22.04.

Passaggio 1: Installazione del web server Nginx

Per visualizzare le pagine web ai visitatori del sito, utilizzerete Nginx, un web server ad alte prestazioni. Utilizzerete il gestore di pacchetti APT per ottenere questo software.

Poiché si tratta della prima volta che utilizzate apt per questa sessione, iniziate aggiornando l'indice dei pacchetti del server:

```bash
sudo apt update
```

A seguire, eseguite sudo apt install per installare Nginx:

```bash
sudo apt install nginx
```

Quando richiesto, premete Y e INVIO per confermare l'installazione di Nginx. Una volta terminata l'installazione, il web server Nginx sarà attivo e funzionante sul vostro server Ubuntu 22.04.

Controllo del firewall

Se avete abilitato il firewall ufw, come raccomandato nella nostra guida di configurazione iniziale del server, dovrete consentire le connessioni a Nginx. Nginx registra alcuni profili UFW diversi durante l'installazione. Per controllare quali profili UFW sono disponibili, eseguite:

```bash
sudo ufw app list
```

Output

```
Applicazioni disponibili:
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
  OpenSSH
```

Abilitare il profilo HTTP

Si consiglia di abilitare il profilo più restrittivo che consenta comunque il traffico necessario. Poiché in questa guida non avete configurato SSL per il vostro server, dovrete consentire solo il traffico HTTP regolare sulla porta 80.

Abilitate questa funzione eseguendo il seguente comando:

```bash
sudo ufw allow 'Nginx HTTP'
```

Verificare lo stato del firewall

Potete verificare la modifica controllando lo stato:

```bash
sudo ufw status
```

Output

```
Stato: attivo

Da        Azione      Da dove
--         ------      ----
OpenSSH    CONSENTI   Ovunque
Nginx HTTP CONSENTI   Ovunque
OpenSSH (v6)CONSENTI   Ovunque (v6)
Nginx HTTP (v6)CONSENTI   Ovunque (v6)
```

Testare il server web

Con la nuova regola del firewall aggiunta, potete testare se il server è attivo e funzionante accedendo al nome di dominio o all'indirizzo IP pubblico del vostro server nel vostro browser web.

Trovare l'indirizzo IP pubblico

Se non avete un nome di dominio puntato al vostro server e non conoscete l'indirizzo IP pubblico del vostro server, potete trovarlo eseguendo uno dei seguenti comandi:

```bash
ip addr show
hostname -I
```

Questo stamperà alcuni indirizzi IP. Potete provare ciascuno di essi a turno nel vostro browser web.

In alternativa, potete controllare quale indirizzo IP è accessibile, come visualizzato da altre località su Internet:

```bash
curl -4 icanhazip.com
```

Scrivete l'indirizzo che ricevete nel vostro browser web e vi porterà alla pagina di destinazione predefinita di Nginx:

http://server_domain_or_IP

Se ricevete questa pagina, significa che avete installato correttamente Nginx e abilitato il traffico HTTP per il vostro web server.

Passaggio 2: Installazione di MySQL

Ora che avete un web server in funzione, dovete installare il sistema di database per memorizzare e gestire i dati del vostro sito. MySQL è un popolare sistema di gestione di database utilizzato all'interno degli ambienti PHP.

Anche in questo caso, utilizzate apt per acquisire e installare questo software:

```bash
sudo apt install mysql-server
```

## Installazione di MySQL (continuazione)

Configurazione di MySQL

Al termine dell'installazione, il servizio MySQL sarà in esecuzione, ma è necessario eseguire alcune configurazioni di sicurezza iniziali.

Impostare la password di root

Eseguite il seguente comando per avviare l'utility di configurazione di MySQL:

```bash
sudo mysql_secure_installation
```

Rispondete alle domande come segue:

 Imposta la password di root per MySQL? (Predefinito: Nessuna) -> *Inserisci una password complessa e memorizzala in modo sicuro.**
 Rimuovi gli utenti anonimi? (Predefinito: Sì) -> *Premi Invio per accettare il valore predefinito.**
 Disabilita l'accesso root da remoto? (Predefinito: Sì) -> *Premi Invio per accettare il valore predefinito.**
 Rimuovi il database di test e accedi a esso? (Predefinito: Sì) -> *Premi Invio per accettare il valore predefinito.**
 Ricarica i privilegi della tabella? (Predefinito: Sì) -> *Premi Invio per accettare il valore predefinito.**

Con queste impostazioni, hai configurato una base di dati MySQL sicura.

Passaggio 3: Installazione di PHP

Per elaborare i codici PHP e creare pagine web dinamiche, è necessario installare il pacchetto PHP e i moduli necessari.

Installazione di PHP e dei moduli base

```bash
sudo apt install php8.1-fpm php8.1-cli php8.1-common php8.1-mbstring php8.1-xml php8.1-json
```

Verificare la versione di PHP

Per assicurarsi che l'installazione sia andata a buon fine, verificate la versione di PHP:

```bash
php -v
```

Output

```
PHP 8.1.1 (cli) (built: Jan 26 2024 14:26:33) ( NTS )
Copyright (c) The PHP Group
Zend Engine v4.1.1, Copyright (c) Zend Technologies
```

Configurazione di PHP

Il file di configurazione principale di PHP è /etc/php/8.1/fpm/php.ini. Potete modificare questo file per personalizzare il comportamento di PHP.

Alcune impostazioni da controllare:

* memory_limit - Limita la quantità di memoria che un singolo script PHP può utilizzare.
* upload_max_filesize - Limita la dimensione massima dei file caricati tramite PHP.
* max_execution_time - Limita il tempo massimo di esecuzione di uno script PHP.

Per ulteriori informazioni su come configurare PHP, consultate la documentazione ufficiale:

https://www.php.net/manual/en/ini.php

Passaggio 4: Creare un file di test PHP

Per testare l'installazione di PHP, create un file di test chiamato info.php nella directory /var/www/html.

```bash
sudo nano /var/www/html/info.php
```

Inserisci il seguente codice nel file:

```php
<?php

phpinfo();

?>
```

Salvate il file e uscite dall'editor.

Aprire il file nel browser web

Aprite il vostro browser web e navigate al seguente URL:

http://server_domain_or_IP/info.php

Dovreste vedere una pagina che mostra informazioni sulla vostra configurazione PHP.

Conclusione prima parte

Con questo, avete installato con successo uno stack LEMP completo sul vostro server Ubuntu 22.04. Ora siete pronti per iniziare a sviluppare e distribuire applicazioni web PHP.


