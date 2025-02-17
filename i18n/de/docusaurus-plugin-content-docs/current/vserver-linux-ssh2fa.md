---
id: vserver-linux-ssh2fa
title: Zwei Faktor Authentifizierung für Linux Server einrichten
description: Informationen, wie du Zwei Faktor Authentifizierung für deinen Linux vServer von ZAP-Hosting einrichten kannst - ZAP-Hosting.com Dokumentation
sidebar_label: Zwei Faktor Authentifizierung
---

## Google Authenticator Installieren

Als erstes musst du den Google Authenticator auf deinem Linux vServer/Rootserver installieren.
Kopiere dir dafür einfach diese Zeile:

```
sudo apt install libpam-google-authenticator
```

Danach wirst du aufgefordert werden "Y" einzugeben um das Packet zu installieren, gib dies ein, drücke Enter und dann ist der Google Authenticator installiert!

![](https://user-images.githubusercontent.com/61839701/166183702-67a07bf3-e199-4f20-a166-9fed0f297d1c.pn)

## Google Authenticator Ausführen

Führe nun den Google Authenticator aus, indem du `google-authenticator` eingibst.

![](https://user-images.githubusercontent.com/61839701/166183758-869cf62d-a242-4365-9ca3-3bbeda553870.png)

Du wirst nun gefragt ob du es ausführen möchtest, schreibe dort auch einfach "Y"

Du kriegst nun einen großen QR Code.
Achte darauf das dein Fenster groß genug für den QR Code ist, ansonsten Drücke "CTRL+C" und gib es erneu ein.
 Öffne deine Authenticator App auf deinem Smartphone und scanne den QR Code.
Für dieses Beispiel benutzen wir den Google Authenticator.

![image](https://user-images.githubusercontent.com/13604413/159171815-4a7368da-fab1-4284-9c90-e310a577dbbf.png)

Stelle sicher das du dir die Backup Codes kopierst, diese können jeweils einmal eingesetzt werden im Fall das du deinen Authenticator verlierst.

![](https://user-images.githubusercontent.com/61839701/166183779-055a3e93-1040-460a-a589-366d9c8bdedf.png)

Jetzt kannst du in der App bereits die Codes sehen mit denen du dich später einloggst.

In unserem Fall sieht das jetzt so aus:

![](https://user-images.githubusercontent.com/61839701/166183807-7e5e04a2-8da6-4d2e-bd3b-a42c2baa045a.png)

Du wirst nun 4 Dinge gefragt.

1. Soll die Google Authenticator Konfiguration gespeichert werden?
2. Soll nur ein Login alle 30 Sekunden möglich sein? (Also nur ein Login alle 30 Sekunden).
3. Soll die Zeit erhöt werden in der ein Code nutzbar ist?
4. Sollen nur Drei Logins alle 30 Sekunden möglich sein? (Schutz gegen Brute Force)

Aus Sicherheitsgründen empfehlen wir bei allen 4 Fragen "Y" einzugeben und dann Enter zu drücken.

![](https://user-images.githubusercontent.com/61839701/166183833-fdb07942-51c2-446e-b87b-e0e0d839e6d4.png)

## Google Authenticator Konfigurieren

Nun müssen wir noch anpassen das der Google Authenticator auch genutzt wird.

Dafür sind nur zwei Dateiänderungen nötig.

### /etc/ssh/sshd_config

In der `/etc/ssh/sshd_config` aktivieren wir die erforderlichen Module.

Öffne die `/etc/ssh/sshd_config` Datei indem du 
```
sudo nano /etc/ssh/sshd_config
``` 
eingibst.

Du bist nun in einem Text Editor. Du kannst dich mit den Pfeiltasten bewegen, frei Text löschen und Eingeben und schliesslich mit `CTRL + X` dann `Y` und letzlich `Enter` die Datei Speichern.

Stelle nun Sicher das die beiden Zeilen `UsePAM` und `ChallengeResponseAuthentication` auf `yes` stehen. So wie hier:

![](https://user-images.githubusercontent.com/61839701/166183852-ea0dc9dd-b5db-40eb-b90e-6c53e71d0908.png)

![](https://user-images.githubusercontent.com/61839701/166183859-2fe15906-efec-4c62-b24f-a1b0fc364ebd.png)

Speichere danach die Datei mit `CTRL + X` dann `Y` und letzlich `Enter`

Starte danach SSH neu mit 
```
sudo systemctl restart ssh
``` 
Wenn keine Fehler kommen gehen wir zum nächsten Schritt.

### /etc/pam.d/sshd

In der `/etc/pam.d/sshd` fügen wir den Google Authenticator dem Login hinzu.

Öffne die `/etc/pam.d/sshd` Datei indem du `sudo nano /etc/pam.d/sshd` eingibst.

Der letzte Schritt ist nun an das Ende der Datei runterzuscrollen und `auth required pam_google_authenticator.so` einzutragen.

![](https://user-images.githubusercontent.com/61839701/166183887-7a683371-6ff5-4260-9300-4f79bcc29e60.png)

Speichere danach die Datei mit `CTRL + X` dann `Y` und letzlich `Enter`

## Einloggen mit 2FA

Jetzt ist die Zeit gekommen sich das erste mal mit 2FA einzuloggen.

Nachdem du die oben genannten Schritte befolgt hast musst du nurnoch deine SSH Verbindung neustarten.

![image](https://user-images.githubusercontent.com/13604413/159171829-90fb3349-c238-4558-818a-0657b87062e5.png)

Du gibst nun ganz normal dein Passwort ein. Danach wirst du nach einem Code gefragt, gebe dort einfach den aktuellen 2FA Code ein.

![](https://user-images.githubusercontent.com/61839701/166183906-b2d6e770-66fa-4096-a642-b3873470dc85.png)

Und schon bist du eingeloggt! Du hast nun 2FA für SSH aktiviert.
