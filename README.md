# Google Chrome Driver

Ce Driver est utilisé par Selenium sur lequel s'appuie Behat pour effectuer
les tests fonctionnels de nos sites Internet.

Note : Pour fonctionner, ce driver necessite que Google Chrome soit installé sur votre ordinateur (dans le répertoire par défaut d'installation).

## Installation

`composer require cregnier/google-chrome-driver`

### Version actuelle

- Nom de la version : ChromeDriver v2.32 (https://chromedriver.storage.googleapis.com/index.html?path=2.32/)
- Support des versions : v59-V61

### Configuration avec Behat

```yaml   
default:
    autoload: src/
    extensions:
        Behat\Symfony2Extension: ~
        Behat\MinkExtension:
            goutte:
                guzzle_parameters:
                    verify: false
            selenium2:
                browser: chrome
                capabilities:
                    browserName: googlechrome
                    browser: chrome
            base_url: https://votre-projet.com/
            files_path: './src/AppBundle/Features/Data/'
            default_session: selenium2
    suites:
        default:
            contexts:
                - AppBundle\Features\Context\FeatureContext
            type: symfony_bundle
            bundle: AppBundle

```

Il faut ajouter les lignes ci-dessous dans votre fichier build.xml (configuration de Phing)

A ajouter après la balise <project ...>
```xml
    <property name="chromedriver.win" value="vendor/bin/chromedriver_win.exe"/>
    <property name="chromedriver.unix" value="vendor/bin/chromedriver_linux" />
    <property name="chromedriver.mac" value="vendor/bin/chromedriver_mac" />
```
Remplacer le bloc <targer name="tests:run:selenium" ...> par :

```xml
    <target name="tests:run-selenium" description="Run Selenium Server">
        <if>
            <os family="mac" />
            <then>
                <exec command="vendor/bin/selenium-server-standalone -Dwebdriver.chrome.driver=${chromedriver.mac}" spawn="true"/>
            </then>
            <elseif>
                <os family="Unix" />
                <then>
                    <exec command="vendor/bin/selenium-server-standalone -Dwebdriver.chrome.driver=${chromedriver.unix}" spawn="true"/>
                </then>
            </elseif>
            <else>
                <exec command="vendor/bin/selenium-server-standalone -Dwebdriver.chrome.driver=${chromedriver.win}" spawn="true"/>
            </else>
        </if>
    </target>
```

## Branche à utiliser

Le dépôt reste maintenu sur la branche dev-master

## Maintenir ce dépôt

Pour maintenir ce dépôt, il suffit de se rendre sur la page suivante : 
https://chromedriver.storage.googleapis.com/index.html

Vous devez télécharger les nouvelles versions du driver pour chaque plateforme.

Une fois les versions téléchargées et décompressées, mettre les fichiers correspondant dans le dossier bin en supprimant les anciens.

### Issues

Si vous rencontrez une erreur, merci d'en faire part dans une issue.

### Problèmes possibles

- Navigateur qui s'ouvre pas en plein écran :

Vous devez ajouter dans votre Context les lignes ci-dessous : 

```php   
/**
 * @BeforeStep
 */
public function beforeStep()
{
    $this->getSession()->getDriver()->maximizeWindow();
}
```

Si vous aviez auparavant dans cette fonction `$this->getSession()->maximizeWindow('current');` ,
vous devez supprimer cette ligne car elle n'est pas prise en compte par le navigateur Google Chrome.


- Les tests ne s'exécutent pas

Si dans la barre du navigateur, vous aperçevez "data;" et que la fenêtre reste sur cet état, 2 cas sont possibles :

- **Le driver n'a pas été trouvé**

Pour régler ce problème, vérifiez que dans le fichier build.xml, vous ayez ces propriétés :
```xml
    <property name="chromedriver.win" value="vendor/bin/chromedriver_win.exe"/>
    <property name="chromedriver.unix" value="vendor/bin/chromedriver_linux" />
    <property name="chromedriver.mac" value="vendor/bin/chromedriver_mac" />
```

- **Le driver n'est plus à jour**

Pour cela, vous devez vérifier que la version de Google Chrome sur votre ordinateur est supportée par la version du Driver.
