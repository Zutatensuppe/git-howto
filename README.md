# GIT Howto (PHPStorm + Command line)

## Clone (Remote Repository in den lokalen Workspace) 

### PHPStorm

	VCS -> Checkout from Version Control -> GitHub

Git Repository URL: [git@github.com:Zutatensuppe/git-howto.git](git@github.com:Zutatensuppe/git-howto.git)  
(Ordner wie gewünscht angeben)  
-> Clone  


### Command line

	git clone git@github.com:Zutatensuppe/git-howto.git FOLDER
	cd FOLDER
	

## Existierende Branches anzeigen

### PHPStorm

	VCS -> Git -> Branches...
	
Unterteilung ist local/remote, standardmäßig befindet man sich im lokalen Branch `master`.

### Command line

	git branch -va




## STASH + UNSTASH

### PHPStorm
stash

	VCS -> Git -> Stash Changes

unstash

	VCS -> Git -> UnStash Changes


### Command line

stash

	git stash

unstash

	git stash apply // übernimmt die Daten im stash, entfernt die Daten im stash aber nicht
	git stash pop // übernimmt die Daten im stash und entfernt die Daten aus dem stash


## Updaten (pull) => svn equivalent: svn up

### PHPStorm

	VCS -> Git -> Pull...
	
Update Type: *Merge* 
Clean working tree before update: *Using Stash*

*Hinweis: Stash ist eine Mechanik von git, Shelve ist eine Mechanik von PHPStorm. Es ist egal, welche man benutzt.*

Danach wird das Update ausgeführt, falls man eine unsaubere Arbeitskopie hatte, kann man die gestashten Änderungen wie folgt wieder herstellen:

	VCS -> Git -> UnStash Changes
	
Bzw. falls als Clean working tree before update Methode Shelve gewählt wurde:

	"Version Control" Fenster (View -> Tool Windows -> Version Control) -> Shelf-Tab wählen -> Rechtsklick auf den gewünschten Eintrag -> Unshelve Changes


### Command line

*Hinweis: In git 2++ kann einfach nur git pull ausgeführt werden. wenn man in git < 2 git pull macht, werden alle Branches geupdatet.*
	
	git pull origin BRANCHNAME




## Feature Branches

### PHPStorm

Als erstes wird `develop` als lokale Kopie geholt, falls noch nicht vorhanden:

	VCS -> Git -> Branches -> origin/develop -> Checkout as new local branch -> develop
	
Wenn ein Remote Branch gewählt ist, wird er automatisch mit tracking zum Remote Branch versehen 
(wenn z.B. Änderungen am Remote Branch vorliegen, weist git vor einem push darauf hin, dass erstmal die Änderungen gepullt werden müssen)  
Man befindet sich nun im lokalen `develop` Branch.  
Jetzt wird der Feature Branch aus dem `develop` angelegt.

Falls man sich gerade im lokalen `develop` Branch befindet:  
	
	VCS -> Git -> Branches -> New Branch -> FEATURENAME
	
Falls man sich gerade nicht im lokalen `develop` Branch befindet:

	VCS -> Git -> Branches -> "develop -> origin/develop" -> Checkout as new branch -> FEATURENAME

### Command line

	git checkout develop
	git branch FEATURENAME



## In einen anderen Branch wechseln

### PHPStorm

	VCS -> Git -> Branches -> FEATURENAME -> checkout

### Command line

	git branch FEATURENAME



## Mergen von Feature "FEATURE" nach develop

Hinweis: Auch `rebase` mergt Branches, allerdings auf andere Art und Weise, bei Interesse: [merging vs rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing/conceptual-overview)  
Imo sollte immer `merge` genutzt werden und nicht `rebase`, siehe dazu auch Andrew Arnott's Antwort auf Stackoverflow: [when to use git rebase instead of git merge](http://stackoverflow.com/questions/804115/when-do-you-use-git-rebase-instead-of-git-merge)  
Bzw. nur `rebase` lokal, dann in `develop`/`master` mergen.

### PHPStorm

	VCS -> Git -> Branches -> "develop -> origin/develop" -> checkout
	VCS -> Git -> Merge Changes -> "FEATURE" auswählen (ggf: No fast forward anhaken, entspricht der option --no-ff auf der kommandozeile)

Änderungen von FEATURE sind jetzt im lokalen `develop` Branch und müssen noch zum origin (z.B. GitHub) gepusht werden.

	VCS -> Git -> Push...



### Command line

	git checkout develop
	git merge FEATURE
	// bzw: git merge --no-ff FEATURE

Änderungen von FEATURE sind jetzt im lokalen `develop` Branch und müssen noch zum origin (z.B. GitHub) gepusht werden.
	
	git push origin develop




## Mergen von aktuellen Änderungen aus develop nach Branch "FEATURE"

### PHPStorm

	VCS -> Git -> Branches -> "FEATURE" -> checkout
	VCS -> Git -> Merge Changes -> "develop" auswählen

### Command line

	git checkout FEATURE
	git merge develop

Änderungen von `develop` sind jetzt im lokalen Branch "FEATURE"












## Änderungen in einen Branch einchecken (hier am Beispiel Branch "FEATURE")

### PHPStorm

	VCS -> Commit Changes (files auswählen, commit message angeben) -> Commit

### Command line
Dateien hinzufügen zum commit:

	git add FILENAME1
	git add FILENAME2
	git add FILENAME3
	git commit -m "Commit Message"

Nach dem Commit sind die Änderungen noch nicht an origin (z.B: GitHub) übermittelt!



## Änderungen an Github senden (PUSH)



### PHPStorm

	VCS -> Git -> Push... 

### Command line

	git push origin BRANCHNAME







## Feature Entwicklung an einem Beispiel (Bugfixes Artikeldetailseite)


### PHPStorm

Falls man sich gerade im lokalen `develop` Branch befindet:
	
	VCS -> Git -> Branches -> New Branch -> artikel-detailseite

Falls man sich gerade nicht im lokalen `develop` Branch befindet:
	
	VCS -> Git -> Branches -> "develop -> origin/develop" -> Checkout as new branch -> artikel-detailseite

Code Ändern:

	// CODE ändern

Wenn fertig mit allen Code-Änderungen, werden die Änderungen in den aktuellen Branch eingecheckt:
	
	VCS -> Commit Changes (files auswählen, commit message angeben) -> Commit




************************************************************************  
*GIGA/KINO spezifisch START*  


Danach Änderungen zur Abnahme in `staging` mergen:
	
	VCS -> Git -> Branches -> "staging" -> checkout
	VCS -> Git -> Merge Changes -> "artikel-detailseite" wählen -> Merge
	
Änderungen sind jetzt im lokalen `staging` Branch und müssen noch zum origin (z.B. GitHub) gepusht werden.

	VCS -> Git -> Push...

Auf Staging-Server:

	git pull origin staging

Auf Abnahme warten

*GIGA/KINO spezifisch ENDE*  
************************************************************************  

Danach Änderungen in `develop` mergen:

	VCS -> Git -> Branches -> "develop" -> checkout
	VCS -> Git -> Merge Changes -> "artikel-detailseite" wählen -> Merge
	
Änderungen sind jetzt im lokalen `develop` Branch und müssen noch zum origin (z.B. GitHub) gepusht werden.

	VCS -> Git -> Push...

Fertig, aber noch nicht live!

Live stellen aller Änderungen in `develop`

	VCS -> Git -> Branches -> "master" -> checkout
	VCS -> Git -> Merge Changes -> "remotes/origin/develop" wählen -> Merge   // oder den lokalen develop vorher mit dem origin develop abgleichen (pull) und dann hier einfach "develop" wählen

Änderungen sind jetzt im lokalen `master` Branch und müssen noch zum origin (z.B. GitHub) gepusht werden.

	VCS -> Git -> Push...

Auf Live-Server:

	git pull origin master

Danach normaler Deploy-Vorgang





### Command line

	git checkout develop
	git branch artikel-detailseite
	git checkout artikel-detailseite // in den Branch wechseln

Code ändern

	// CODE ändern

Änderungen hinzufügen

	git add FILENAME1 
	git add FILENAME2
	git add FILENAME3
	git commit -m "nachricht"


************************************************************************  
*GIGA/KINO spezifisch START*  

Danach Änderungen zur Abnahme in `staging` mergen:

	git checkout staging
	git merge artikel-detailseite
	git push origin staging

Auf Staging-Server:

	git pull origin staging

Auf Abnahme warten

*GIGA/KINO spezifisch ENDE*  
************************************************************************  


Danach Änderungen in `develop` mergen:

	git checkout develop
	git merge artikel-detailseite
	git push origin develop

Fertig, aber noch nicht live!

Live stellen aller Änderungen in `develop`
`develop` updaten

	git checkout develop 
	git pull origin develop
	
`develop` in `master` mergen

	git checkout master
	git merge develop
	git push origin master

Auf Live-Server:
	
	git pull origin master
	
Danach normaler Deploy-Vorgang








## QUICKFIX (dirty) an einem Beispiel (Bugfixes Hotfix-GIGA)

Falls man sich gerade im lokalen `master` Branch befindet:
ggf: `VCS -> Git -> Pull...`
	
	VCS -> Git -> Branches -> New Branch -> hotfix-giga
	
Falls man sich gerade nicht im lokalen `develop` Branch befindet:

	VCS -> Git -> Branches -> "master -> origin/master" -> Checkout as new branch -> hotfix-giga

Code ändern

	// CODE ändern

Wenn fertig mit allen Code-Änderungen, werden die Änderungen in den aktuellen Branch eingecheckt:

	VCS -> Commit Changes (files auswählen, commit message angeben) -> Commit

Änderungen in `master` mergen

	VCS -> Git -> Branches -> "master" -> checkout
	VCS -> Git -> Merge Changes -> "hotfix-giga" wählen -> Merge
	
Änderungen sind jetzt im lokalen `master` Branch und müssen noch zum origin (z.B. GitHub) gepusht werden.

	VCS -> Git -> Push...

Auf Live-Server:
	
	git pull origin master
	
Danach normaler Deploy-Vorgang.  

Danach müssen die Quickfixes in `develop` gemergt werden:

************************************************************************  
*GIGA/KINO spezifisch START*  

	VCS -> Git -> Branches -> "staging" -> checkout
	VCS -> Git -> Merge Changes -> "remotes/origin/master" wählen -> Merge
	VCS -> Git -> Push...

Auf Staging-Server:

	git pull origin staging

*GIGA/KINO spezifisch ENDE*  
************************************************************************  

	VCS -> Git -> Branches -> "develop" -> checkout
	VCS -> Git -> Merge Changes -> "remotes/origin/master" wählen -> Merge
	VCS -> Git -> Push...





### Command line

	git checkout master
	// ggf: git pull origin master

	git branch quickfix-giga
	git checkout quickfix-giga

Code ändern

	// CODE ändern

Änderungen hinzufügen und einchecken

	git add FILENAME1 
	git add FILENAME2
	git add FILENAME3
	git commit -m "nachricht"

Änderungen in `master` mergen und zum origin gepusht werden.

	git checkout master
	git merge hotfix-giga
	git push origin master

Auf Live-Server:

	git pull origin master
	
Danach normaler Deploy-Vorgang.  

Danach müssen die Quickfixes in `develop` gemergt werden:


************************************************************************  
*GIGA/KINO spezifisch START*  

	git checkout staging
	git merge master
	git push origin staging

Auf Staging-Server:

	git pull origin staging

*GIGA/KINO spezifisch ENDE*  
************************************************************************  
	
	git checkout develop
	git merge master
	git push origin develop
	
