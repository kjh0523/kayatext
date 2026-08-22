# kayatext

[한국어](README.md) ·
[Deutsch](README.de.md) ·
[English](README.en.md) ·
**Français** ·
[日本語](README.ja.md) ·
[Русский](README.ru.md) ·
[中文](README.zh.md)

**Extrait, à partir de documents HWP (traitement de texte coréen), Excel et Word, du
texte qu'une IA peut lire.**

[Télécharger](../../releases/latest) · [Signaler un bogue](../../issues) ·
[Autres outils](https://github.com/kjh0523/kayautils)

Ce dépôt sert à la distribution. Il ne contient pas de source, seulement des exécutables
([pourquoi](#pourquoi-il-ny-a-pas-de-source)).

---

## Pourquoi cet outil

Avant de donner un document à une IA, il faut en faire du texte. **Avec HWP, c'est
impossible.** Les extracteurs abondent pour docx et pdf ; pour les documents coréens, il
n'y en a presque aucun — et **la quasi-totalité des documents administratifs coréens sont
en HWP**.

Extraire les seuls caractères ne fait que la moitié du travail. **La qualité de la
recherche dépend de la survie de la structure** : un tableau aplati en prose se retrouve
mal, et sans titres il n'y a aucune frontière où découper le document.

La sortie par défaut est donc du **Markdown**. Les tableaux sortent en tableaux `|`, les
feuilles de calcul en titres `##`. L'écosystème découpe déjà le Markdown tel quel : cela
s'insère directement dans les chaînes de traitement existantes.

```
un document administratif de 116 pages → 193 000 caractères de Markdown · 91 tableaux
```

Les 91 tableaux survivent tous. En extraction texte brut, chacun d'eux devient de la
prose.

---

## Utilisation

Depuis le dossier décompressé (macOS · Linux)

```bash
./kayatext compte-rendu.hwp            # produit compte-rendu.md
./kayatext *.hwp *.xlsx -o out/        # plusieurs fichiers vers un même dossier
./kayatext document.hwp --txt          # texte brut (abandonne la structure)
./kayatext document.hwp -              # vers la sortie standard (pour les tubes)
./kayatext --version
./kayatext --licenses                  # mentions open source
```

Sous PowerShell (Windows), il faut `.\` et l'extension.

```powershell
.\kayatext.exe compte-rendu.hwp
.\kayatext.exe *.hwp *.xlsx -o out\
.\kayatext.exe document.hwp --txt
.\kayatext.exe --version
```

Placé dans le `PATH`, le `./` initial disparaît — voir
[L'appeler depuis n'importe où](#lappeler-depuis-nimporte-où).

**Un fichier en échec n'arrête pas le traitement.** Les échecs sont rassemblés et
signalés à la fin, et le code de sortie vaut 1 s'il y en a eu au moins un — un script
doit pouvoir en juger.

### Formats acceptés

`.hwp` `.hwpx` `.hml` `.xlsx` `.xlsm` `.docx` `.rtf`

Le RTF n'est lu que jusqu'aux paragraphes — si l'original comporte des tableaux, ils
ressortent en prose. Ce n'est pas nous qui l'avons jeté : l'analyseur ne le fournit pas,
et le résultat le précise.

`.pdf` `.pptx` `.doc` ne sont pas encore pris en charge.

---

## Téléchargement

| | |
|---|---|
| **macOS (Apple Silicon)** | ✅ [`kayatext-macos-arm64.tar.gz`](../../releases/latest) |
| **Windows (x64)** | ✅ [`kayatext-windows-x64.zip`](../../releases/latest) |
| Linux (x64, musl) | en préparation |
| macOS (Intel) | plus tard |

L'archive contient quatre éléments.

```
kayatext        l'exécutable
rhwp            le convertisseur HWP — sans lui, pas de .hwp
LICENSE.txt
THIRD-PARTY-NOTICES.md
```

**Aucune installation.** Déplacez le dossier entier où vous voulez ; il suffit que `rhwp`
soit à côté.

**macOS · Linux**

```bash
tar xzf kayatext-macos-arm64.tar.gz
cd kayatext-macos-arm64
./kayatext --version
```

**Windows** — PowerShell ne cherche pas dans le dossier courant, d'où le `.\`. Dans
`cmd.exe`, `kayatext` seul suffit.

```powershell
cd kayatext-windows-x64
.\kayatext.exe --version
```

### L'appeler depuis n'importe où

Dans le `PATH`, vous pouvez omettre le `./`. **Gardez les deux fichiers ensemble** — sans
`rhwp` à côté, `.hwp` ne fonctionne pas.

```bash
sudo cp kayatext rhwp /usr/local/bin/          # macOS · Linux
```

```powershell
# Windows — déplacer tout le dossier et l'ajouter au PATH est plus simple
move kayatext-windows-x64 C:\tools\kayatext
setx PATH "%PATH%;C:\tools\kayatext"
```

> **Il n'est pas signé, le système vous arrêtera donc une fois.** C'est un projet
> personnel et aucun certificat de développeur n'y est encore attaché. **Le fichier n'est
> pas corrompu.**
>
> - **macOS** — Réglages Système → Confidentialité et sécurité → « Ouvrir quand même »
> - **Windows** — dans la fenêtre bleue SmartScreen, « Informations complémentaires » →
>   « Exécuter quand même »

---

## Licence

**Ce n'est pas de l'open source.** Le texte intégral est dans le `LICENSE.txt` de
l'archive.

**Gratuit pour tout le monde** — particuliers comme entreprises, administrations, écoles,
associations, à usage commercial comme non commercial.

**Aucune limitation de fonctionnalité.** Il n'existe pas d'édition payante ; c'est la
seule version distribuée. Aucun code n'y compte votre usage ni ne verrouille de
fonction.

### La licence entreprise est facultative

**Achetez-la ou non. Le programme ne change pas.**

Certaines organisations doivent **prouver sur papier qu'elles ont le droit d'utiliser un
logiciel** avant qu'un examen d'achat ou un audit ne passe. « C'est gratuit, alors on
l'utilise » ne suffit pas dans ce cadre. Pour ces structures, il y a **un certificat de
licence, une facture et un support prioritaire**. **Cela n'ajoute aucune
fonctionnalité.**

**La redistribution est interdite.** Cela inclut déposer l'archive là où d'autres peuvent
la récupérer — dossier partagé interne, serveur de fichiers, bibliothèque documentaire
d'un intranet, service d'hébergement de fichiers, disque en nuage avec le partage activé.
Pour en parler autour de vous, transmettez l'adresse de ce dépôt plutôt que le fichier.
Si vous avez réellement besoin de le diffuser dans votre organisation, ouvrez une issue :
des conditions particulières peuvent être convenues.

**Le texte extrait n'est soumis à aucune restriction.** Il est entièrement à vous.

### Mentions open source

```bash
./kayatext --licenses
```

Le texte intégral figure aussi dans `THIRD-PARTY-NOTICES.md`, dans l'archive.

La prise en charge du HWP repose sur [rhwp](https://github.com/edwardkim/rhwp) (MIT).
Sans ce projet, cet outil n'existerait pas.

---

## Pourquoi il n'y a pas de source

C'est un outil fait par une seule personne, et j'ai choisi d'empêcher la redistribution.
Quand il n'existe qu'un seul endroit où l'obtenir, je sais quelle version circule, et un
correctif atteint réellement les gens qui ont rencontré le problème.

Mes contributions à d'autres projets open source, et les raisons qui les motivent, sont
publiées.

---

## Signalez-moi les documents qui échouent

Certains bogues ne se montrent jamais dans des fichiers fabriqués pour les tests. **Ils
n'existent que dans les documents réels** — une fusion sur 11 colonnes, un tableau où se
glissent des formes, un formulaire fait avec une vieille version du traitement de texte.

### Ce qui aide le plus

**Des documents publics d'administrations ou d'organismes publics.** Avis, formulaires,
communiqués — inutile d'envoyer le fichier, **l'adresse de téléchargement suffit**.

### S'il s'agit d'un document d'entreprise — ne le publiez pas

Les documents de travail contiennent des noms et des coordonnées, et **l'auteur subsiste
jusque dans les propriétés du fichier**. Une fois déposé dans un lieu public, c'est
irréversible, et cela peut vous mettre en difficulté.

**Décrivez seulement le symptôme.** Quelque chose comme « une lettre administrative de
3 pages avec des tableaux ; à partir de la page 2 les filets disparaissent » me suffit
pour fabriquer un fichier similaire et reproduire le problème. Si le fichier est vraiment
nécessaire, nous l'échangeons en privé et je vous explique comment l'anonymiser.

### Un remerciement

Si ce que vous envoyez attrape effectivement un bogue ou entre dans les tests de
non-régression —

- votre nom figure sur la liste des tests de non-régression (si vous le souhaitez)
- **un certificat de licence entreprise vous est offert.** Vous n'en avez pas besoin pour
  vous en servir — c'est gratuit, en entreprise aussi. Il ne vaut quelque chose que pour
  ceux qui **doivent produire des papiers** lors d'un examen d'achat ou d'un audit

Ne vous forcez pas à envoyer des fichiers pour obtenir un certificat.
**Une adresse vaut mieux que dix fichiers.**

---

Copyright © 2026 가야태자 (kjh0523). All rights reserved.
