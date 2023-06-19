# Doodba un cas pràctic de 0 a 100

Crearé un doodba de proves.eccit.com i el faré arribar a producció.
Allà gestionaré backup, restauraré i faré alguna cosa més

## Somh-hi

### Creem el bucket i accés
Primer cal crear un bucket i un usuari per accedir-hi
AWS
Crear Bucket (pendent estudiar el cicle de vida)
Crear Usuari (IAM) Amb política:

```
{
    "Version":"2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:ListAllMyBuckets",
            "Resource": "arn:aws:s3:::*"
        },
        {
            "Effect": "Allow",
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::BUCKET_NAME",
                "arn:aws:s3:::BUCKET_NAME/*"
            ]
        }
    ]
}
```



### Llencem el copier

```
quim@eccit-desktop:~/Dev/proves$ mkdir proves_doodba
quim@eccit-desktop:~/Dev/proves$ cd proves_doodba
quim@eccit-desktop:~/Dev/proves/proves.eccit.com$ copier -r HEAD copy gh:Tecnativa/doodba-copier-template .
🎤 On which odoo version is it based?
   14.0
🎤 ⚠️ Using a misconfigured proxy for production can create a security hole. Using none can create performance problems.
Which proxy will you use to deploy odoo?
   Traefik
🎤 If you want to initialize Odoo automatically in a specific language, write it here. The format must be ll_CC where ll is the language code and CC is the country code.
Examples: en_US, es_ES, es_VE...
   ca_ES
🕵️ 💡 To auto-generate strong passwords, see https://ddg.gg/?q=password+64+strong
⚠️ This password is critical for security, especially if you set odoo_listdb to true, so keep it safe.
What will be your odoo admin password?
   **********************
🎤 Do you want to list databases publicly?
   No
🎤 If you are using an OCI/Docker image registry (such as the Docker Hub, Quay or Gitlab registry) to publish the Odoo images that will be built with this Doodba project, specify here the path to the odoo image
Example: docker.io/myteam/example-odoo
 
🎤 Tell me who you are.
If private modules do not include this author, pylint will warn you.
   eccIT
🎤 What's your project name?
Do not use dots or spaces in the name; just "A-Za-z0-9-_" please.
   proves_eccit_com
🎤 It's important to use a good license for your project.
In https://choosealicense.com/ you can read details about most common FOSS ones. In https://www.odoo.com/documentation/user/14.0/legal/licenses/licenses.html you can find other propietary licenses we support in
So, what's your project's license?
   Boost Software License 1.0
🎤 If you host this project in Gitlab, then please enter here the project URL.
It must have no trailing slash.
💡 If you don't use Gitlab, leave this empty and ignore all other Gitlab questions.
Example: `https://gitlab.com/Tecnativa/your-doodba-project`.
 
🎤 Configure the production domains for this project.

The format is a list with these keys:

- hosts: The domain names. List of strings.
- path_prefixes: The path prefixes (if any) that will match this routing rule.
  List of strings. Default: [].
- redirect_to: Apply a redirection to this other domain. By default, it will
  be a 302 redirection, but it can be turned into a 301 redirection by enabling
  the `redirect_permanent` option.
  String. Default: null.
- redirect_permanent: Return 301 instead of 302 in a Traefik redirection, making it permanent.
  Boolean. Default: False.
- cert_resolver: Traefik cert resolver name. String/Boolean. Default: "letsencrypt".
  Set it to `true` to use a self-signed certificate. Set it to `false` to
  disable TLS in those domains. If not `false`, it will force using HTTPS.
- entrypoints: List of entrypoint names to use when creating this rule. Default: [].
  Leave empty to apply this rule on all available entrypoints. Those are defined
  in your Traefik deployment.

See https://www.tecnativa.com/r/Anu for an example.

⚠ Remember that you have to write one-line YAML, at least until Copier
supports multiline input

⚠ Some advanced features are only available if you deploy with Traefik 2.

💡 The 1st host from the 1st domain that has no `path_prefixes` will be considered
the main one. In the example above, it'd be `www.main.com`.
    (Finish with 'Alt+Enter' or 'Esc then Enter')
> - hosts: proves.eccit.com
 
🎤 Configure the test domains for this project.

The format is the same as for `prod_domains`; a list with these keys:

- hosts: The domain names. List of strings.
- path_prefixes: The path prefixes (if any) that will match this routing rule.
  List of strings. Default: [].
- redirect_to: Apply a redirection to this other domain. By default, it will be a
  302 redirection, but it can be turned into a 301 redirection by enabling the
  `redirect_permanent` option.
  String. Default: null.
- redirect_permanent: Return 301 instead of 302 in a Traefik redirection, making it permanent.
  Boolean. Default: False.
- cert_resolver: Traefik cert resolver name. String/Boolean. Default: "letsencrypt".
  Set it to `true` to use a self-signed certificate. Set it to `false` to
  disable TLS in those domains. If not `false`, it will force using HTTPS.
- entrypoints: List of entrypoint names to use when creating this rule. Default: [].
  Leave empty to apply this rule on all available entrypoints. Those are defined
  in your Traefik deployment.

See https://www.tecnativa.com/r/Y0v for an example.

⚠ Remember that you have to write one-line YAML, at least until Copier
supports multiline input

⚠ Some advanced features are only available if you deploy with Traefik 2.

💡 The 1st host from the 1st domain that has no `path_prefixes` will be considered
the main one. In the example above, it'd be `demo1.main.com`.
    (Finish with 'Alt+Enter' or 'Esc then Enter')
> -hosts: provestest.eccit.com
 
🎤 Tell me the list of paths which where you want to forbid crawlers.
Imagine you do not want your `/shop` and `/shop/**` pages to be indexed. Then put here `[/shop]`.
⚠️ It must be a list. And this is only supported if you deploy with Traefik.
💡 We will convert this to `Path` rules. Check valid syntax in https://docs.traefik.io/routing/routers/#rule
    (Finish with 'Alt+Enter' or 'Esc then Enter')
> - /web
  - /website/info
🎤 If you need to whitelist certain CIDR to allow only them to access your Odoo instance, set that here please.
⚠️ It must be a list. And this is only supported if you deploy with Traefik 2+.
    (Finish with 'Alt+Enter' or 'Esc then Enter')
>
🎤 Which PostgreSQL version do you want to deploy? (Recommended: for new instances always use the latest version. Version 9.6 has no backup support.)
   15
🎤 Which user name will be used to connect to the postgres server?
   odoo14
🎤 💡 If database names differ among environments, operations like restoring a database from an alien environment will be harder to maintain, but can provide additional security. It's up to you. We default to "p
What is going to be the main database name?
   proves.eccit.com
🕵️ What will be your postgres user password?
   *******************
🎤 Do you want to expose database service?
   No
🎤 Set your Odoo db filter. It must be a regexp that matches the domain name being visited. It is useful if you use Odoo in SaaS mode. Only applied to production environment.
 
🎤 Now, let's start configuring outgoing mail.
In case an email coming out from odoo doesn't have a valid `From:` header address, which address should be the default one that sends the email?                                                                   
                                                                                                                                                                                                                   
Execution stopped by user
quim@eccit-desktop:~/Dev/proves/proves.eccit.com$ copier -r HEAD copy gh:Tecnativa/doodba-copier-template .
🎤 On which odoo version is it based?
   14.0
🎤 ⚠️ Using a misconfigured proxy for production can create a security hole. Using none can create performance problems.
Which proxy will you use to deploy odoo?
   Traefik
🎤 If you want to initialize Odoo automatically in a specific language, write it here. The format must be ll_CC where ll is the language code and CC is the country code.
Examples: en_US, es_ES, es_VE...
   ca_ES
🕵️ 💡 To auto-generate strong passwords, see https://ddg.gg/?q=password+64+strong
⚠️ This password is critical for security, especially if you set odoo_listdb to true, so keep it safe.
What will be your odoo admin password?
   **********************
🎤 Do you want to list databases publicly?
   No
🎤 If you are using an OCI/Docker image registry (such as the Docker Hub, Quay or Gitlab registry) to publish the Odoo images that will be built with this Doodba project, specify here the path to the odoo image
Example: docker.io/myteam/example-odoo
 
🎤 Tell me who you are.
If private modules do not include this author, pylint will warn you.
   eccIT
🎤 What's your project name?
Do not use dots or spaces in the name; just "A-Za-z0-9-_" please.
   proves-eccit-com
🎤 It's important to use a good license for your project.
In https://choosealicense.com/ you can read details about most common FOSS ones. In https://www.odoo.com/documentation/user/14.0/legal/licenses/licenses.html you can find other propietary licenses we support in
So, what's your project's license?
   Boost Software License 1.0
🎤 If you host this project in Gitlab, then please enter here the project URL.
It must have no trailing slash.
💡 If you don't use Gitlab, leave this empty and ignore all other Gitlab questions.
Example: `https://gitlab.com/Tecnativa/your-doodba-project`.
 
🎤 Configure the production domains for this project.

The format is a list with these keys:

- hosts: The domain names. List of strings.
- path_prefixes: The path prefixes (if any) that will match this routing rule.
  List of strings. Default: [].
- redirect_to: Apply a redirection to this other domain. By default, it will
  be a 302 redirection, but it can be turned into a 301 redirection by enabling
  the `redirect_permanent` option.
  String. Default: null.
- redirect_permanent: Return 301 instead of 302 in a Traefik redirection, making it permanent.
  Boolean. Default: False.
- cert_resolver: Traefik cert resolver name. String/Boolean. Default: "letsencrypt".
  Set it to `true` to use a self-signed certificate. Set it to `false` to
  disable TLS in those domains. If not `false`, it will force using HTTPS.
- entrypoints: List of entrypoint names to use when creating this rule. Default: [].
  Leave empty to apply this rule on all available entrypoints. Those are defined
  in your Traefik deployment.

See https://www.tecnativa.com/r/Anu for an example.

⚠ Remember that you have to write one-line YAML, at least until Copier
supports multiline input

⚠ Some advanced features are only available if you deploy with Traefik 2.

💡 The 1st host from the 1st domain that has no `path_prefixes` will be considered
the main one. In the example above, it'd be `www.main.com`.
    (Finish with 'Alt+Enter' or 'Esc then Enter')
> - host: proves.eccit.com
 
🎤 Configure the test domains for this project.

The format is the same as for `prod_domains`; a list with these keys:

- hosts: The domain names. List of strings.
- path_prefixes: The path prefixes (if any) that will match this routing rule.
  List of strings. Default: [].
- redirect_to: Apply a redirection to this other domain. By default, it will be a
  302 redirection, but it can be turned into a 301 redirection by enabling the
  `redirect_permanent` option.
  String. Default: null.
- redirect_permanent: Return 301 instead of 302 in a Traefik redirection, making it permanent.
  Boolean. Default: False.
- cert_resolver: Traefik cert resolver name. String/Boolean. Default: "letsencrypt".
  Set it to `true` to use a self-signed certificate. Set it to `false` to
  disable TLS in those domains. If not `false`, it will force using HTTPS.
- entrypoints: List of entrypoint names to use when creating this rule. Default: [].
  Leave empty to apply this rule on all available entrypoints. Those are defined
  in your Traefik deployment.

See https://www.tecnativa.com/r/Y0v for an example.

⚠ Remember that you have to write one-line YAML, at least until Copier
supports multiline input

⚠ Some advanced features are only available if you deploy with Traefik 2.

💡 The 1st host from the 1st domain that has no `path_prefixes` will be considered
the main one. In the example above, it'd be `demo1.main.com`.
    (Finish with 'Alt+Enter' or 'Esc then Enter')
> - hosts: provestest.eccit.com
 
🎤 Tell me the list of paths which where you want to forbid crawlers.
Imagine you do not want your `/shop` and `/shop/**` pages to be indexed. Then put here `[/shop]`.
⚠️ It must be a list. And this is only supported if you deploy with Traefik.
💡 We will convert this to `Path` rules. Check valid syntax in https://docs.traefik.io/routing/routers/#rule
    (Finish with 'Alt+Enter' or 'Esc then Enter')
> - /web
  - /website/info
 
🎤 If you need to whitelist certain CIDR to allow only them to access your Odoo instance, set that here please.
⚠️ It must be a list. And this is only supported if you deploy with Traefik 2+.
    (Finish with 'Alt+Enter' or 'Esc then Enter')
>
🎤 Which PostgreSQL version do you want to deploy? (Recommended: for new instances always use the latest version. Version 9.6 has no backup support.)
   15
🎤 Which user name will be used to connect to the postgres server?
   odoo14
🎤 💡 If database names differ among environments, operations like restoring a database from an alien environment will be harder to maintain, but can provide additional security. It's up to you. We default to "p
What is going to be the main database name?
   proves_eccit
🕵️ What will be your postgres user password?
   *******************
🎤 Do you want to expose database service?
   No
🎤 Set your Odoo db filter. It must be a regexp that matches the domain name being visited. It is useful if you use Odoo in SaaS mode. Only applied to production environment.
   ^proves_eccit
🎤 Now, let's start configuring outgoing mail.
In case an email coming out from odoo doesn't have a valid `From:` header address, which address should be the default one that sends the email?
   quim@eccit.com
🎤 ⚠️ If you leave this answer empty, all next SMTP settings will be ignored.
If you supply a valid SMTP host, production Odoo will be able to send emails without needing to configure any `ir.mail_server` record, because Doodba will configure it to use a mail relay that will manage a loca
So, what is your SMTP host?
Example: mail.example.com
   smtp.gmail.com
🎤 Indicate the port to connect in the SMTP server you just defined.
⚠️ NEVER use port 465 👉 https://github.com/tomav/docker-mailserver/issues/1428
   587
🎤 Indicate the user to connect in the SMTP server you just defined.
For Odoo to work fine, this user needs to be able to do mail spoofing.
   quim@eccit.com
🕵️ What is your SMTP password?
   *********************
🎤 Doodba uses docker-mailserver as the local mail relay.
Which version of the image do you want to use? You can check which ones are available in https://github.com/orgs/docker-mailserver/packages/container/docker-mailserver/versions
   10
🎤 Usually, if you send mails like "user@example.com", the canonical domain would be "example.com".
This canonical domain should have correct SPF, DKIM and DMARC settings that allow the SMTP host to send mails in its name.
When Odoo tries to send a mail that does not come from a canonical domain, the domain you indicate here will be used when rewriting the address with SRS (https://en.wikipedia.org/wiki/SRS).
What's your canonical domain?
   eccit.com
🎤 Supply a list of other domains authorized to send email from this Odoo instance and SMTP host. They will not be affected by SRS. They also must have valid SPF, DKIM and DMARC settings.
You do not need to repeat the canonical domain you indicated above.
Example: [examplemail.com, example.org]
 
🎤 If you want to use an Amazon S3 bucket, write its URL like `boto3+s3://example_bucket[/example/path]` to make sure it works fine.
If you want to use any other backend, supply any URL supported by Duplicity (our choice backup engine; read http://duplicity.nongnu.org/vers8/duplicity.1.html#sect7 for those URL formats).
If you don't want backups, leave this empty.
Where should the backups be stored?
   boto3+s3://backup-proves-eccit-doodba/dia
🎤 Doodba uses the docker-duplicity image to make the backups.
Which version of the image do you want to use? You can check which ones are available in https://github.com/orgs/Tecnativa/packages/container/docker-duplicity-postgres/versions and https://github.com/orgs/Tecnat
   latest
🎤 The backup container will send email reports if the SMTP relay is properly configured.
What email address should it use to send them?
   quim@eccit.com
🎤 Where to send those backup reports?
   quim@eccit.com
🎤 If you're using S3, you probably want to delete outdated backups using bucket lifecycle rules. If you use other storage backend, then you probably want to enable outdated backups deletion using duplicity itse
So, do you want to enable duplicity backup deletion via cron?
   No
🎤 Set the timezone used by the backup cron for reports.
Visit https://www.cyberciti.biz/faq/linux-unix-set-tz-environment-variable/ to know how to obtain a valid value for this variable.
   UTC+2
🕵️ If you're using AWS S3 to store backups, provide here your access key ID.
   ********************
🕵️ If you're using AWS S3 to store backups, provide here your secret access key.
   ****************************************
🕵️ ⚠️ This passphrase is critical for security, so keep it safe. You will need it to restore backups.
Which will be your backups passphrase?
   *************************

Copying from template version 5.1.4.post12.dev0+9e48836
 identical  .
    create  .editorconfig
    create  .docker
    create  .docker/backup.env
    create  .docker/db-creation.env
    create  .docker/db-access.env
    create  .docker/smtp.env
    create  .docker/odoo.env
    create  .pylintrc-mandatory
    create  .vscode
    create  .vscode/extensions.json
    create  .vscode/doodba.code-snippets
    create  tasks.py
    create  .flake8
    create  devel.yaml
    create  setup-devel.yaml
    create  .isort.cfg
    create  common.yaml
    create  .gitignore
    create  .module-readme.rst.j2
    create  test.yaml
    create  .eslintrc.yml
    create  prod.yaml
    create  .pre-commit-config.yaml
    create  LICENSE
    create  .prettierrc.yml
    create  odoo
    create  odoo/.dockerignore
    create  odoo/custom
    create  odoo/custom/build.d
    create  odoo/custom/build.d/.empty
    create  odoo/custom/dependencies
    create  odoo/custom/dependencies/apt_build.txt
    create  odoo/custom/dependencies/apt.txt
    create  odoo/custom/dependencies/gem.txt
    create  odoo/custom/dependencies/pip.txt
    create  odoo/custom/dependencies/npm.txt
    create  odoo/custom/src
    create  odoo/custom/src/addons.yaml
    create  odoo/custom/src/private
    create  odoo/custom/src/private/.editorconfig
    create  odoo/custom/src/repos.yaml
    create  odoo/custom/ssh
    create  odoo/custom/ssh/id_rsa.pub
    create  odoo/custom/ssh/config
    create  odoo/custom/ssh/known_hosts
    create  odoo/custom/ssh/id_rsa
    create  odoo/custom/entrypoint.d
    create  odoo/custom/entrypoint.d/.empty
    create  odoo/custom/conf.d
    create  odoo/custom/conf.d/.empty
    create  odoo/Dockerfile
    create  .copier-answers.yml
    create  README.md
    create  .pylintrc

 > Running task 1 of 2: invoke after-update
 > Running task 2 of 2: invoke develop
consell: S'està utilitzant «master» com a nom de la branca inicial. Aquest nom de branca
consell: per defecte es pot canviar. Per a configurar el nom inicial de la branca que
consell: s'utilitzarà en tots els repositoris nous, i que suprimirà aquest avís, useu:
consell: 
consell: 	git config --global init.defaultBranch <nom>
consell: 
consell: Els noms més usats habitualment en lloc de «master» són «main», «trunk» i
consell: «development». La branca acabada de crear es pot canviar de nom amb l'ordre:
consell: 
consell: 	git branch -m <nom>
S'ha inicialitzat un repositori buit del Git en /home/quim/Dev/proves/proves.eccit.com/.git/
pre-commit installed at .git/hooks/pre-commit
```



## Què tenim aquí?
Dins la carpeta ens ha creat una estructura. El més còmode és tancar i obrir el workspace que hi ha a l'arrel del ldirectori amb el VSCode. Així xuta tot millor

## I ara?

```
invoke img-build --pull
invoke git-aggregate
```

Només tenir en compte que qun a'hagi de fer algun canvi en el ssh/config cal refer el build perque amb el git-aggregate no es recalcula aquest fitxer (Lògic, només agrega tot allò que fa falta)






