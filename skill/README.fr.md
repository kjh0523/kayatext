# Une compétence pour les agents IA

[한국어](README.md) ·
[Deutsch](README.de.md) ·
[English](README.en.md) ·
**Français** ·
[日本語](README.ja.md) ·
[Русский](README.ru.md) ·
[中文](README.zh.md)

Demandez à un outil comme Claude de lire un document coréen et **il écrira son propre
analyseur**. `.hwp` est un format binaire : ce qui revient est en général du charabia,
ou un document dont les tableaux ont disparu.

Cette compétence lui dit d'**appeler `kayatext` à la place**.

---

## Il faut d'abord kayatext

La compétence n'est qu'une consigne ; c'est `kayatext` qui fait la conversion.

[Télécharger](../../../releases/latest) → décompressez et placez-le dans le `PATH`.
Pour que `.hwp` fonctionne, `rhwp` doit être **dans le même dossier**.

```bash
sudo cp kayatext rhwp /usr/local/bin/
kayatext --version
```

---

## Installation

### Claude Code

```bash
mkdir -p ~/.claude/skills/korean-documents
curl -sL -o ~/.claude/skills/korean-documents/SKILL.md \
  https://raw.githubusercontent.com/kjh0523/kayatext/main/skill/SKILL.md
```

Pour ne l'utiliser que dans un projet, placez-le sous le `.claude` de ce projet plutôt
que sous `~/.claude`.

### claude.ai

Réglages → Skills, puis téléversez `SKILL.md`.

---

## Vérifier que ça marche

Dans une nouvelle session, demandez la lecture d'un document coréen.

```
Résume-moi ce fichier : compte-rendu.hwp
```

**Si la compétence s'est déclenchée**, elle appelle `kayatext compte-rendu.hwp -`.
**Sinon**, l'outil tentera d'ouvrir le fichier avec `olefile` en Python — c'est le signe
que la compétence n'est pas installée.

---

## Ce qu'elle demande à l'agent

- pour `.hwp` `.hwpx` `.hml` `.xlsx` `.xlsm` `.docx` `.rtf` `.pdf`, **ne pas écrire
  d'analyseur** — appeler `kayatext`
- **utiliser Markdown par défaut.** En texte brut, les tableaux deviennent de la prose —
  un document administratif de 116 pages en comptait 91
- si `kayatext` est absent, **le dire à l'utilisateur.** Ne pas écrire un analyseur à la
  place et rendre un résultat abîmé — ce n'est pas de l'aide, c'est une réponse fausse
- s'il manque quelque chose à la conversion (formes, graphiques…), **le signaler
  aussi.** Si un tableau présent dans le document manque au résultat, il ne doit pas
  répondre comme s'il l'avait lu

Les deux derniers points sont les plus importants. **L'empêcher de faire semblant de
savoir** : voilà l'échec que cette compétence cherche vraiment à éviter.

---

## Ce qui ne marche pas encore

`kayatext` ne sait pas lire `.pptx` ni `.doc` (l'ancien format), et la compétence
le dit.

**Le PDF, lui, est lu** — les tableaux avec filets et les titres survivent ; les
tableaux sans filets sortent en texte courant.

Un serveur MCP est en préparation. L'objectif est de pouvoir s'en servir via `npx`, sans
installation.
