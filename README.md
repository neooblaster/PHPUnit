# 1. Introduction

PHPUnit est un framework de test pour les programmes développés orienté objets. Les fonctionnalités sont nombreuses, mais on va se concentré sur le rôle principale dans un premier temps qui est l'execution de tests unitaires

# 2. Pré-requis

Je vous laisse le soin de procéder à l'installation des pré-requis suivant pour dérouler la suite de cette documentation

* Avoir installer PHP

# 3. Installation

## 3.1. Installer PHPUnit

La version de PHPUnit dépendera directement de la version PHP qui est installée sur votre serveur.

Ci-dessous, les différentes versions de PHPUnit et les versions PHP supportées.

* PHPUnit 6.1. :: Version PHP 7.0 et 7.1
* PHPUnit 5.7. :: Version PHP 5.6, 7.0 et 7.1
* PHPUnit 4.8. :: Version PHP 5.3 à 5.6

Pour connaitre la version PHP d'installer, il suffit simplement de lancer la commande suivante :

```bash
php -v

# Doit renvoyer quelque chose de similaire :
PHP 5.6.30-0+deb8u1 (cli) (built: Apr 14 2017 16:20:58)
Copyright (c) 1997-2016 The PHP Group
Zend Engine v2.6.0, Copyright (c) 1998-2016 Zend Technologies
    with Xdebug v2.5.4, Copyright (c) 2002-2017, by Derick Rethans
    with Zend OPcache v7.0.6-dev, Copyright (c) 1999-2016, by Zend Technologies
```

Sur la base de la version qui est installée chez moi, c'est-à-dire la version `5.6.30`, je vais installer la version 5.7 de PHPUnit.

Pour avoir l'executable PHPUnit, il suffit de télécharger l'archive PHP `phpunit-5.7.phar` à l'adresse suivante à l'aide de la commande ci-dessous :

```bash
wget https://phar.phpunit.de/phpunit-5.7.phar
```

Pour simplifier l'utilisation, nous allons rendre la commande executable en copiant l'archive à l'aide de la commande suivante :

```bash
sudo cp phpunit-5.7.phar /usr/local/bin/phpunit
sudo chmod 755 /usr/local/bin/phpunit
```

Pour savoir si PHPUnit fonctionne correctement, il suffit simplement de taper `phpunit` afin d'obtenir le manuel d'aide.

## 3.2. Installer Xdebug

PHPUnit nécessite la présence de l'extension Xdebug. Pour connaitre la version à installer, Xdebug fourni un outil de customisation en ligne à l'adresse suivante : https://xdebug.org/wizard.php

Dans le champs de saisie de texte, il faudra copier le résultat obtenu par la commande :

```bash
php -i
```

Bien que Xdebug fourni les instructions pour procéder à l'installation, je vais les reprendres.

En ce qui me concerne, la version qui m'a été conseillée était la version "2.5.4"

Pour commencer, il faut télécharger l'archive Xdebug

```bash
wget http://xdebug.org/files/xdebug-2.5.4.tgz
```

Puis l'extraire

```bash
tar -xzvf xdebug-2.5.4.tgz
```

Les fichiers extraits ont été placer dans le dossier `xdebug-2.5.4` dans lequel il faudra se positionner à l'aide de la commande 

```bash
cd xdebug-2.5.4
```

Pour les besoins de l'installation, il faut lancer la commande suivante :

```bash
phpize

# Résultat
Configuring for:
PHP Api Version:         20131106
Zend Module Api No:      20131226
Zend Extension Api No:   220131226
```

Important : La valeur obtenue pour `Zend Module Api No` est importante et servira dans la suite de ce setup. Notons là : `20131106`

Il ne reste plus qu'à compiler l'extension à l'aide de la commande suivante :

```bash
./configure && make
```

Une fois compilé, il faut envoyer le module dans l'emplacement suivant :

```bash
sudo cp modules/xdebug.so /usr/lib/php5/20131226
```

Enfin, il ne reste plus qu'à ajouter le module à la configuration de PHP puis de relancer le service.


```bash
# Editez le fichier /etc/php5/cli/php.ini
nano /etc/php5/cli/php.ini

# Puis ajoutez-y la ligne suivante à l'endroit de votre choix (la où c'est le plus judicieux)
zend_extension = /usr/lib/php5/20131226/xdebug.so

# Redémarrer le service PHP
sudo service php5-fpm restart
```

# 4. "Hello World" du test

Pour mettre les bases de travail, nous allons faire un équivalent "Hello World" dans le monde du test unitaire.

Avant de commencer, il faut savoir à ce jour que la documentation est rédigé pour la version 6.1 de PHPUnit et que l'accès à la classe PHP Fournie à changé.

Ayant une ancienne version d'installé et pour l'historique, je vais instancié le test par la vieille syntaxe. La nouvelle syntaxe utilise les shortcuts à l'aide du mot clé `use`.

Créez le fichier `test.php` et placez-y le code suivant :

```php
<?php
class StackTest extends PHPUnit_Framework_TestCase
{
    public function testPushAndPop()
    {
        $stack = [];
        $this->assertEquals(0, count($stack));

        array_push($stack, 'foo');
        $this->assertEquals('foo', $stack[count($stack)-1]);
        $this->assertEquals(1, count($stack));

        $this->assertEquals('foo', array_pop($stack));
        $this->assertEquals(0, count($stack));
    }
}
?>
```

* Vieille syntaxe : `class StackTest extends PHPUnit_Framework_TestCase`
* Nouvelle syntaxe : `class StackTest extends TestCase`

Il ne reste plus qu'à lancer le "Hello World" du test :

```bash
phpunit test.php

# Résultat
PHPUnit 4.8.35 by Sebastian Bergmann and contributors.

.

Time: 1.15 seconds, Memory: 8.00MB

OK (1 test, 5 assertions)
```

Voilà, les tests fonctionnents parfaitement. Il ne reste plus qu'à customiser les nombreux paramètres existant !


# 5. Fichier de configuration

Comme je l'ai dis plutôt, **PHPUnit** offre une grandes quantité de fonctionnalité. L'exploitation de ces fonctionnalités peut devenir lourd en ligne de commande. Si dans le dossier courant, la présence d'un fichier `phpunit.xml.dist` ou `phpunit.xml` est présent, alors celui-ci sera lu est traitera la configuration présente. Le fichier `phpunit.xml` est prioritaire sur le fichier `phpunit.xml.dist`.

Il existe deux arguments pour la ligne de commande :
   * `--configuration <file>` ou encore `-c <file>`
   * `--no-configuration`
   
Si l'argument `--configuration <file>` est spécifié, alors le fichier indiqué sera chargé est primera sur `phpunit.xml.dist` et `phpunit.xml`.
Si l'argument `--no-configuration`, aucun des fichiers de configuration par défaut ne sera chargé.

Le fichier `phpunit.xml` permet de spécifier une liste de fichier PHP à executé dans lesquels se trouvent des tests unitaire. Il est aussi possible de spécifier des dossiers à lire. C'est valable également pour faire des exclusions.

Voici le fichier `phpunit.xml` dans sa configuration minimales requise, équivalent à la commande.

```bash
phpunit test.php
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit>
    <testsuites>
        <testsuite>
           <file>test.php</file>
        </testsuite>
    </testsuites>
</phpunit>
```

**Important** : En l'absence du block `<testsuites></testsuites`, il faudra spécifier un fichier à executer dans lequel se trouve les tests. Si vous ne le faites pas, le manuel sera affiché et laissera pensé que cela ne fonctionne pas.

**note** : Il n'est pas inutile d'indique le doctype XML. Question de rigeur.
