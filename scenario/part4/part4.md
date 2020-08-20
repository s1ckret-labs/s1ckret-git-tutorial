# Part 4. Merging 

## A. Gist of mergining
Добро пожаловать на курс по системе контроля версий Git. 
В этой главе мы рассмотрим понятие и стратегии слияния веток(branch merge). Вы уже знаете, что каждую новую функцию продукта нужно разробатывать на новой ветке. Теперь мы научимся объединять результат отдельных фич в единный продукт. А также научимся решать конфликты при слиянии веток.

### Merge strategies
Существует множество стратегий слияния. Git применяет их самостоятельно. Мы рассмотрим две самые часто-встречаеммые стратегии:

- ускоренное слияние (a.k.a. Fast-forward merge)
- трёхстороннее слияние (a.k.a. True merge, Recursive Merge)

#### Fast-forward merge

Рассмотрим следующую историю:
```
  * b8557ab (HEAD, feature-first-hero) feat: add hero
  * 23662ee feat: create object interfeice
 /
* 78bad59 (master) feat: implement delta-time game loop1
* 69ac846 feat: create window context
* ef107eb initial commit
```
Если Вы внимательно посмотрите, то история коммитом линейна.
Для сливания ветки `feature-first-hero` в `master`, git применит fast-forward стратегию. А то есть, просто продвинет ветку `master` до ветки `feature-first-hero`. Отсюда и название стратегии.

История после слияния будет выглядить так:

```
* b8557ab (HEAD, maseter, feature-first-hero) feat: add hero
* 23662ee feat: create object interfeice
* 78bad59 feat: implement delta-time game loop1
* 69ac846 feat: create window context
* ef107eb initial commit
```

#### Recursive Merge

Рассмотрим следующую историю:
```
* 519a303 (HEAD -> feature-html) index.html: fix typo
| * 2542f8a (feature-css) style.css: add style for buttons
| * 3f30f1d add style.css
|/
* e2a9fb4 index.html: add markup for tutorial
* ca596a8 add index.html
```

Теперь, история нелинейная.
Для сливания ветки `feature-css` в `feature-html`, git применит recursive(трёх-стороннее слияние) стратегию. А то есть, создаст __коммит слияния__, у которого будет более чем один предок(в нашем случае 2 предка).

> Почему 'трёхсторонее'? Стратегия использует три коммита: 2 последних коммита веток, что сливаются(519a303, 2542f8a), и 1 коммит - общий предок (e2a9fb4).

После слияния история репозитория выглядит так:
```
*   7f5ad36 (HEAD -> feature-html) Merge branch 'feature-css' into feature-html
|\
| * 2542f8a (feature-css) style.css: add style for buttons
| * 3f30f1d add style.css
* | 519a303 index.html: fix typ
|/
* e2a9fb4 index.html: add m
```

> У коммита может быть много предков! Посмотрите стратегию слияния octupus! 

### Merge confilicts
Git не всегда может сделать всё самостоятельно. Иногда, когда вы сливаете две ветки и некоторая строчка была изменена в каждой ветке возникает __merge conflict__. Обычно это происходит, когда на проекте работает более чем один разработчик.

При потытке слияния двух конфликтующий ветки, Git сообщит про конфликт. Вам прийдётся решить его самому. Как это делать мы разберём на практике.
```
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

## B. Commands

### git merge

Для того чтобы влить ветку `feature-css` в ветку `feature-html` переключитесь на ветку `feature-html`, а потом пропишите команду `git merge feature-css`. После этого git постарается соединить две ветки. 
Вы можете явно указать стратегию с помощью флага `-s`. По стандарту git использует recursive strategy. Примеры:

```
git merge -s resolve feature-css
git merge -s recursive feature-css
git merge -s octopus feature-css-1 feature-css-2 feature-randomizer
```

Подробнее про каждую стратегию мы рассказываем в платном курсе.


## C. Practice
#### Fast-forward merge

Представим следующую ситуацию. Вы разрабатываете игру. Вы уже создали окно и главный цикл. Вы переключились на новую ветку _feature-first-hero_, где создали героя и добавили ему управление. История коммитов может выглядить примерно так:
```
  * b8557ab (HEAD, feature-first-hero) feat: add hero
  * 23662ee feat: create object interfeice
 /
* 78bad59 (master) feat: implement delta-time game loop1
* 69ac846 feat: create window context
* ef107eb initial commit
```
Вы всё протестировали и решаете, что ветку `feature-first-hero` можно вливать в `master`.
```
# Comands

# Switching to the branch
git checkout master
# or
git switch master

# Merging
git merge feature-first-hero
```

Давайте посмотрим что сделает git. Он применит fast-forward стратегию. А то есть, просто перенесёт новые коммиты на ветку `master`.

История после слияния будет выглядить так:

```
* b8557ab (HEAD, maseter, feature-first-hero) feat: add hero
* 23662ee feat: create object interfeice
* 78bad59 feat: implement delta-time game loop1
* 69ac846 feat: create window context
* ef107eb initial commit
```

#### Recursive Merge

Представим следующую ситуацию. Вы разрабатываете веб-приложение. Вы создали разметку на главной странице. Вы переключились на новую ветку _feature-css_ для создание стилей. Но вдруг вы заметили опечатку в разметке. Вы переключились на ветку обратно и сделали коммит-поправку.

После история репозитория выглядит так:
```
* 519a303 (HEAD -> feature-html) index.html: fix typo
| * 2542f8a (feature-css) style.css: add style for buttons
| * 3f30f1d add style.css
|/
* e2a9fb4 index.html: add markup for tutorial
* ca596a8 add index.html
```

Вы решили влить ветку _feature-css_ в _feature-html_. Поскольку git не может просто перенести коммиты, git использует стратегию под название трёх-стороннее слияние(recursive merge).

```
# Comands

# Switching to the branch
git checkout feature-html
# or
git switch feature-html

# Merging
git merge feature-css
```

> Почему 'трёхсторонее'? Стратегия использует три коммита: 2 последних коммита веток, что сливаются(519a303, 2542f8a), и 1 коммит - общий предок (e2a9fb4).

В итоге, git самостоятельно создает новый коммит - __коммит слияния__(7f5ad36). Этот коммит отличается от других тем, что у него более чем один предок.

После слияния история репозитория выглядит так:
```
*   7f5ad36 (HEAD -> feature-html) Merge branch 'feature-css' into feature-html
|\
| * 2542f8a (feature-css) style.css: add style for buttons
| * 3f30f1d add style.css
* | 519a303 index.html: fix typo
|/
* e2a9fb4 index.html: add m
```

> У коммита может быть много предков! Посмотрите стратегию слияния octupus! 

### Merge confilicts
Git не всегда может сделать всё самостоятельно. Иногда, когда вы сливаете две ветки и некоторая строчка была изменена в каждой ветке. Обычно это происходит, когда на проекте работает болле чем один разработчик. Давайте поработаем двумя разработчиками. Коммиты первого будем помечать `Dev-Ivan`, коммиты второго - `Dev-Alex`.
История репозитория будет выглядеть так:
```
* 40cb5fe (HEAD -> dev-alex) Dev-Alex: Also modify second line
| * 43688a4 (dev-ivan) Dev-Ivan: Modify second line
|/
* abef87f (master) Initial commit

```
> Файл README.md на ветке `dev-alex`

```
# Well Hello!
I think python is cool!
```

> Файл README.md на ветке `dev-ivan`

```
# Well Hello!
I think C++ is cool!
```

#### Confilct

Иван и Алекс встретились в кафе и начали обсуждать их проект. После долгих дисскусий они пришли в выводу, что С++ и python оба хорошие ЯП. Они решили влить ветку `dev-ivan` в `dev-alex`.

```
# Commands
git checkout dev-alex
git merge dev-ivan
```
Упс, Git сообщает про конфликт!
```
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

Теперь файл `README.md` выглядит так:
```
# Well Hello!
<<<<<<< HEAD
I think python is cool!
=======
I think C++ is cool!
>>>>>>> dev-ivan
```

Итак, мы видим части с двух веток.
`<<<<<< HEAD` - показывает локальные изменения (в нашем случае файл с ветки `dev-alex`).
`======` - разделитель.
`>>>>>> <branch-name>` - показывет изменения ветки `<branch-name>` (в нашем случае `dev-ivan`)

#### How to resolve conflict

Для разрешения конфликта мы просто редактируем файл в текстовом редакторе. Убираем то что нам не нужно. Добавляем, что нужно. В нашем случае мы отредактируем файл так:
```
# Well Hello!
I think python and C++ is cool!
>>>>>>> dev-ivan
```

После этого вы делаете коммит и готово.

История после слияния выглядит так:
```
*   130882f (HEAD -> dev-alex) Merge branch 'dev-ivan' into dev-alex
|\
| * 43688a4 (dev-ivan) Dev-Ivan: Modify second line
* | 40cb5fe Dev-Alex: Also modify second line
|/
* abef87f (master) Initial commit
```

#### Using mergetools for resolving conflicts
Разберём некоторые термины:

`LOCAL` - так называют текущую ветку (в которую вливают).
`REMOTE` - так называют ветку, которую вливают в `LOCAL` ветку.
`BASE` - так называют общего предка веток `LOCAL` и `REMOTE`.
`MERGED` - так называют результат после слияния (то что будет сформировано в новый коммит).

### TODO Describe merge process in vimdiff
### TODO Describe merge process in meld
