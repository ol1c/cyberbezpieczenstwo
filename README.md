**Projekt – Instalacja i aktualizacja certyfikatu Let's Encrypt dla serwera WWW**

---

## 1. Instalacja

    ```bash
    ssh root@<ADRES_IP>
    apt update && apt upgrade -y
    ```

- **Instalacja Apache + Certbot**

  ```bash
  apt install -y apache2 certbot python3-certbot-apache ufw
  ```

- **Instalacja Lighttpd + Certbot**

  ```bash
  apt install -y lighttpd certbot python3-certbot-lighttpd ufw
  ```

```bash
ufw allow OpenSSH
ufw allow 'Apache Full'
ufw allow 80
ufw allow 443
ufw enable
```

3. **Test serwera**

   `http://test.example.pl` powinno wyświetlić stronę Apache.

   ![Niezabezpieczony adres strony](img/niezabezpieczona.png)

---

## 2. Instalacja certyfikatu na serwerach WWW

1. **Wstępne testy w trybie staging**:

   - **Apache**:

     ```bash
     certbot --apache -d twoja-domena.pl --staging --agree-tos --register-unsafely-without-email
     ```

     ![Instalacja testowa w środowisku apache](img/apache-certbot-staging.png)

   - **Lighttpd**:

     ```bash
     certbot certonly --webroot -w /var/www/html -d twoja-domena.pl --staging --agree-tos --register-unsafely-without-email
     ```

     ![Niezabezpieczony adres strony](img/niezabezpieczona!.png)

2. **Test produkcyjny** (usuń `--staging`):

   ```bash
   certbot certonly --webroot -w /var/www/html -d twoja-domena.pl --agree-tos --register-unsafely-without-email
   ```

   ![Instalacja w środowisku apache](img/lighttpd-certbot.png)
   ![Bezpieczna strona](img/bezpieczna.png)

---

## 3. Odnawianie certyfikatu

1. **Automatyczne (`certbot renew`)**

   - Domyślnie zainstalowane cron/systemd timer.
   - Sprawdź: `systemctl status certbot.timer`.

2. **Aktualizacja (odnawianie) certyfikatu**

   - Ręczne odnowienie testowe:

     ```bash
     certbot renew --dry-run
     ```

     ![Odnowa certyfikatu testowa (dry-run)](img/dryrun.png)

   - Sprawdź status odnawiania:

     ```bash
     systemctl status certbot.timer
     ```

   - Jeśli chcesz wymusić natychmiastowe odnowienie (produkcja):

     ```bash
     certbot renew
     ```

   - Po odnowieniu Apache automatycznie przeładuje skonfigurowane serwisy. W razie potrzeby:

     ```bash
     systemctl reload apache2
     ```

3. **Weryfikacja ważności certyfikatu**

   Sprawdź datę wygaśnięcia:

   ```bash
   openssl x509 -enddate -noout < /etc/letsencrypt/live/twoja-domena.pl/cert.pem
   ```

   ![Weryfikacja daty wygaśnięcia](img/enddate.png)

---

## 4. Instalacja w środowisku hostingowym

1. **Automatyczna instalacja przez AutoSSL (np. w cPanel)**

   - Zaloguj się do **panelu administracyjnego** (np. cPanel).

   - Przejdź do sekcji **SSL/TLS Status**.

   - Wybierz domeny → kliknij **Run AutoSSL**.

   - Po kilku minutach certyfikat zostanie zainstalowany automatycznie.

   - Odwiedź `https://twoja-domena.pl`, aby potwierdzić działanie certyfikatu.

2. **Odnawianie certyfikatu na hostingu**

   Jeśli **AutoSSL** jest aktywne, odnawia się samo co 60–90 dni.

---
