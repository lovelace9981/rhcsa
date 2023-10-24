Password expire

https://www.cyberciti.biz/faq/linux-set-change-password-how-to/

Creación de usuarios con caducidad de contrasela 
https://unix.stackexchange.com/questions/80968/how-can-i-create-automatically-expiring-user-accounts

```bash
sudo useradd -g group1 -e `date -d "0 days" +%Y-%m-%d` -p password usuario
```

Obtener id de  los usuario
https://kb.iu.edu/d/adwf
