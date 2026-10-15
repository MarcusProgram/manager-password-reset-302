# manager-password-reset-302

nuclei-шаблон под Evolution CMS: GET на `/manager/index.php?a=0&hash=` с выключенными редиректами, совпадение — 302 с заголовком Location.

Суть баги: `UserHashLogin::process()` из пакета `evocms-services/user-manager` (1.0.26) ищет пользователя через `where('cachepwd', $hash)->first()` и не проверяет hash на пустоту. Пока сброс пароля ни разу не запрашивали, в поле `cachepwd` лежит пустая строка, поэтому пустой hash матчит первого такого пользователя и молча логинит его в менеджер. На свежей установке это id=1, админ. Подтверждено на Evolution CMS Community Edition 3.1.30, подвержены и более ранние версии, где есть login-by-hash путь.

Шаблон находит кандидатов, а не доказывает багу: 302 на этом URL бывает и у обычного редиректа на форму логина. Что реально даёт сессию, проверять руками по кукам. Существующего CVE под этот класс у Evolution CMS на момент проверки не было.

запуск:

```
nuclei -t nuclei_manager_password_reset_302.yaml -l targets.txt
```
