#Base de Données

* Définition de données
* Manipulation de données
* Contrôle de données

*DBA* Database Administrator

##Instructions SQL

###Create Table

Sur Unix -> noms de table et noms de base sont sensibles à la casse mais pas les noms de colonnes ou de contraintes ne le sont pas (de base).  
  
Clefs Primaires et clefs étrangères.
  
Table User avec primary key user_id et Table Facture avec clef étrangère user_id qui fait référence à celle de Table User

* CREATE [TEMPORARY] TABLE database.table
* (colonne1 type1 [NOT NULL | NULL] [AUTO_INCREMENT] [DEFAULT valeur1] [COMMENT "commentaire"], colonne2 type2, ...)
* [CONSTRAINT nomContrainte1 typeContrainte1, ...]
* [ENGINE= InnoDB | MyISAM | ...]
* DROP [TEMPORARY] TABLE IF EXISTS toto [RESTRICT | CASCADE];

#####Contraintes

* CONSTRAINT un_table_champ UNIQUE (colonne1)
* CONSTRAINT PRIMARY KEY (id_user)
* CONSTRAINT FOREIGN KEY (status_id) REFERENCES status (id_status)
* CHECK () // ne sert à rien pour le moment
* INDEX

**Convention**
UNIQUE : un_table_champ  
PRIMARY KEY : pk_table  
FOREIGN KEY : fk_table  
INDEX : idx_table_champ

##Type de colonnes

* Chiffres
    * BOOL
    * TINYINT
    * SMALLINT
    * MEDIUMINT 
    * INT
    * BIGINT
    * FLOAT
    * DOUBLE
    * INT [UNSIGNED] -> forcément positif [ZERO_FILL] -> rajoute des zéros avant
* Textes
    * TINYTEXT
    * TEXT
    * MEDIUMTEXT
    * LONGTEXT
    * CHAR(4) -> obligatoirement 4 caractères
    * VARCHAR(255) -> jusqu'à 255 caractères
* Date
    * DATETIME
    * TIMESTAMP
* Autre
    * ENUM -> permet de définir jusqu'à 65535 options mais possible d'en sélectionner qu'un
    * SET -> permet de définir jusqu'à 64 choix et on peut en selectionner plusieurs
    * BLOB -> stockage d'objets informatiques


**Example** :
``` SQL
CREATE TABLE status(id_status INT, status VARCHAR,
    PRIMARY KEY (id_status)
);

CREATE TABLE user (id_user INT, name VARCHAR, email VARCHAR, status_id INT,  
    CONSTRAINT un_user_email UNIQUE (email),
    PRIMARY KEY (id_user),
    FOREIGN KEY (status_id) REFERENCES status (id_status)
);

CREATE TABLE post
    (id_post INT NOT NULL AUTO_INCREMENT, 
    CA INT(9) NOT NULL UNSIGNED,
    champ1 CHAR(4),
    champ2 VARCHAR(4),
    code_postal INT(5) ZERO_FILL
    title VARCHAR(70) NOT NULL, 
    content LONGTEXT NULL,
    author VARCHAR(70) NOT NULL,
    type_post SET ('article', 'interview', 'journal')
    status ENUM ('valider', 'en cours', 'a venir'),
    date_publication DATETIME NOT NULL,
    CONSTRAINT pk_post PRIMARY KEY (id_post)
);

CREATE INDEX idx_post_author ON post ('author' ASC); 
```

----------------------------------------------------

Table avec ARTICLES
Sur PHP une liste d'article avec titre, contenu short
ensuite contenu, autheur et date de publication
que ceux qui sont validés
un post peut répondre à un autre post

----------------------------------------------------

``` SQL
CREATE TABLE IF NOT EXISTS status (
    id_status INT(9) NOT NULL AUTO_INCREMENT,
    status VARCHAR(30) NOT NULL,
    CONSTRAINT pk_status PRIMARY KEY (id_status),
);

CREATE TABLE IF NOT EXISTS posts (
    id_post INT(9) NOT NULL AUTO_INCREMENT,
    title VARCHAR(70) NOT NULL,
    content LONGTEXT,
    post_date DATETIME NOT NULL,
    status_id INT(9) NOT NULL DEFAULT 1,
    author VARCHAR(70) NOT  NULL,
    post_parent_id INT(9) NULL,
    CONSTRAINT pk_posts 
        PRIMARY KEY (id_post),
    CONSTRAINT fk_status 
        FOREIGN KEY (status_id) 
        REFERENCE status (id_status)
        ON DELETE NO ACTION
        ON UPDATE NO ACTION,
    CONSTRAINT fk_posts_post_parent_id 
        FOREIGN KEY (post_parent_id) 
        REFERENCE posts (id_post)
        ON DELETE NO ACTION
        ON UPDATE NO ACTION
);

ENGINE = InnoDB;

CREATE INDEX idx_posts_author ON posts ('author' ASC);
```

##Manipulation des données

* INSERT [LOW_PRIORITY | HIGH_PRIORITY] [IGNORE] INTO database.ma_table (colonne1, colonne2, ...) VALUES ('valeur1', 3, ...);
* INSERT INTO post (id_post, title, valeur) VALUES (1, 'monTitre', 2) ON DUPLICATE KEY UPDATE valeur = 2, title = 'monTitre';
* REPLACE INTO table (colonne1) VALUES ('valeur1');
* UPDATE database.ma_table SET colonne1 = 'valeur1', colonne2 = valeur2 WHERE id = 4;
* UPDATE ma_table SET colonne1 = valeur1, colonne2 = valeurs2 LIMIT 0,5
* UPDATE ma_table SET colonne1 = valeur1, colonne2 = valeurs2 ORDER BY id_post DESC LIMIT 5
* DELETE [QUICK] FROM ma_table WHERE id_post IN (1,2,3);
* TRUNCATE ma_table
* SELECT * FROM posts
* SELECT id_post, author, title, DATA_FORMAT(post_date, '%d/%m/%Y %H:%i:%s'), EXTRACT(type FROM post_date) FROM posts
* SELECT id_post, author, title, DATA_FORMAT(post_date, '%d/%m/%Y %H:%i:%s'), EXTRACT(type FROM post_date), UNIX_TIMESTAMP(post_date) FROM posts

Low | High Priority -> Attends que la table soit libre ou bien outrepasse les autres opérations en cours  
Ignore -> continue l'insert même si il y a des erreurs  
ON DUPLICATE KEY UPDATE -> au cas ou il y ait déjà une valeur, update la ligne  
LIMIT 0,5 -> commence à 0 et compte 5  
QUICK -> ne reconstruit pas l'index (MyISAM only)  
TRUNCATE -> vide la table (sans la supprimer) (InnoDB garde la structure et les auto_increments la ou ils en etaient);

##Modification des tables

* ALTER TABLE users RENAME TO user;
* RENAME users TO user;
* ALTER TABLE users ADD (colonne1 INT(4), colonne2 VARCHAR(10));
* ALTER TABLE users ADD (colonne1 INT(4), colonne2 VARCHAR(10)) FIRST;
* ALTER TABLE users ADD (colonne1 INT(4), colonne2 VARCHAR(10)) AFTER nom_colonne;
* ALTER TABLE user CHANGE user_email email, user_name name;
* ALTER TABLE user MODIFY email VARCHAR(110) NOT NULL COMMENT 'mon commentaire';
* ALTER TABLE user MODIFY email VARCHAR(110) NOT NULL COMMENT 'mon commentaire' FIRST;
* ALTER TABLE user MODIFY email VARCHAR(110) NOT NULL COMMENT 'mon commentaire' AFTER name;
* ALTER TABLE user DROP [COLUMN] email;
* ALTER TABLE user DROP PRIMARY KEY;
* ALTER TABLE user DROP FOREIGN KEY fk_name_foreign_key;
* ALTER TABLE user DROP INDEX idx_user_index;
* ALTER TABLE user ADD CONSTRAINT pk_user_key PRIMARY KEY (id_user);


Exercice
--------

Creer une table post avec 4 colonnes
id_post, date, content, statut (valider, en cours, non valider)

Rajouter les colonnes autheur, titre (not null), contenu court, catégorie (interview, papier, humeur)

Ajouter une contrainte vers la table statut (creer la table statut)

Ajouter 3 lignes dans posts.

``` SQL
CREATE TABLE IF NOT EXISTS posts (
    id_post INT(9) NOT NULL AUTO_INCREMENT,
    date DATETIME NOT NULL,
    content LONGTEXT,
    status ENUM('valider', 'en cours', 'non valider'),
    CONSTRAINT pk_posts PRIMARY KEY (id_post)
);

ALTER TABLE posts ADD (
    author VARCHAR(50),
    title VARCHAR(255) NOT NULL,
    excerpt TEXT,
    category ENUM('interview', 'papier', 'humeur')
);

CREATE TABLE IF NOT EXISTS status (
    id_status INT(3) NOT NULL AUTO_INCREMENT,
    status_name VARCHAR(30) NOT NULL,
    CONSTRAINT pk_status PRIMARY KEY (id_status)
);

ALTER TABLE posts DROP status;
ALTER TABLE posts ADD (status_id INT(3) NOT NULL);

ALTER TABLE posts ADD CONSTRAINT fk_status_id FOREIGN KEY (status_id) REFERENCES status (id_status);

INSERT INTO status (status_name) VALUES ('valider');
INSERT INTO status (status_name) VALUES ('en cours');
INSERT INTO status (status_name) VALUES ('non valider');

OR

INSERT INTO status (status_name) VALUES ('valider'),('en cours'),('non valider');


INSERT INTO posts (date, content, status_id, author, title, excerpt, category) VALUES (
    "1988-04-12 16:00:00",
    "Lorem ipsum dolor sit amet, consectetur adipisicing elit. Tempore voluptate maxime quaerat beatae, accusantium, repellat molestiae dolor ratione aliquid officia sint, eos hic. Hic iusto debitis, vero a, magnam dolorum?",
    "1",
    "val_pinkman",
    "Where is it?",
    "Lorem ipsum dolor sit amet, consectetur adipisicing elit. Ducimus eos iusto vitae ad delectus voluptatem dolores rerum expedita dicta.",
    "humeur"
);

INSERT INTO posts (date, content, status_id, author, title, excerpt, category) VALUES (
    "2014-04-12 13:00:00",
    "Lorem ipsum dolor sit amet, consectetur adipisicing elit. Tempore voluptate maxime quaerat beatae, accusantium, repellat molestiae dolor ratione aliquid officia sint, eos hic. Hic iusto debitis, vero a, magnam dolorum?",
    "2",
    "wicked_wolf",
    "It's my birthday",
    "Lorem ipsum dolor sit amet, consectetur adipisicing elit. Ducimus eos iusto vitae ad delectus voluptatem dolores rerum expedita dicta.",
    "interview"
);

INSERT INTO posts (date, content, status_id, author, title, excerpt, category) VALUES (
    "2014-09-23 18:30:00",
    "Lorem ipsum dolor sit amet, consectetur adipisicing elit. Tempore voluptate maxime quaerat beatae, accusantium, repellat molestiae dolor ratione aliquid officia sint, eos hic. Hic iusto debitis, vero a, magnam dolorum?",
    "3",
    "dat_kraken_tho",
    "Release me!",
    "Lorem ipsum dolor sit amet, consectetur adipisicing elit. Ducimus eos iusto vitae ad delectus voluptatem dolores rerum expedita dicta.",
    "papier"
);
```

##Select

* SELECT [colonne1, colonne2] FROM table WHERE instruction GROUP BY conlonne1 HAVING instruction ORDER BY colonne1 ASC LIMIT 5
* SELECT COUNT(*) FROM table
* SELECT COUNT(*) alias_count, category FROM table
* WHERE id_post IN (6,7,8,9,10) 
* WHERE id_post BETWEEN 6 AND 12
* WHERE author IS [NOT] NULL
* WHERE author LIKE '%name%'
* WHERE instruction AND instruction2
* ORDER BY colonne1 ASC
* ORDER BY colonne1 DESC, colonne2 DESC
* LIMIT 10
* LIMIT [10,]100

Example
=======

``` SQL
SELECT [colonne1, colonne2] [*]
FROM table, post
WHERE instruction
GROUP BY colonne1 
HAVING instruction
ORDER BY colonne1 ASC || DESC
LIMIT 5 

SELECT id_post, author, title
FROM posts p,
WHERE 1 
AND author LIKE 'gab%';

SELECT COUNT(*) nombre, category
FROM posts
GROUP BY category
HAVING COUNT(*) >= 10
ORDER BY category DESC
LIMIT 100;
```



