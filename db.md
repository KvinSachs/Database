#Base de Données

* Définition de données
* Manipulation de données
* Contrôle de données

*DBA* Database Administrator

##Instructions SQL

####Create Table

Sur Unix -> noms de table et noms de base sont sensibles à la casse mais pas les noms de colonnes ou de contraintes ne le sont pas (de base).  
  
Clefs Primaires et clefs étrangères.
  
Table User avec primary key user_id et Table Facture avec clef étrangère user_id qui fait référence à celle de Table User

* CREATE [TEMPORARY] TABLE database.table
* (colonne1 type1 [NOT NULL | NULL] [AUTO_INCREMENT] [DEFAULT valeur1] [COMMENT "commentaire"], colonne2 type2, ...)
* [CONSTRAINT nomContrainte1 typeContrainte1, ...]
* [ENGINE= InnoDB | MyISAM | ...]

#####Contraintes

* CONSTRAINT un_table_champ UNIQUE (colonne1)
* CONSTRAINT PRIMARY KEY (id_user)
* CONSTRAINT FOREIGN KEY (status_id) REFERENCES status (id_status)
* CHECK () // ne sert à rien pour le moment

**Convention**
UNIQUE : un_table_champ  
PRIMARY KEY : pk_table  
FOREIGN KEY : fk_table  

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
```
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
    type_post SET ('article', 'interview', 'journal')
    status ENUM ('valider', 'en cours', 'a venir'),
    date_publication DATETIME NOT NULL,
    CONSTRAINT pk_post PRIMARY KEY (id_post)
);
```