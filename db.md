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
* (colonne1 type1 [NOT NULL | NULL] [DEFAULT valeur1] [COMMENT "commentaire"], colonne2 type2, ...)
* [CONSTRAINT nomContrainte1 typeContrainte1, ...]
* [ENGINE= InnoDB | MyISAM | ...]

#####Contrainte

* CONSTRAINT un_table_champ UNIQUE (colonne1)
* CONSTRAINT PRIMARY KEY (id_user)
* CONSTRAINT FOREIGN KEY (status_id) REFERENCES status (id_status)
* CHECK () // ne sert à rien pour le moment


Example :  
```
CREATE TABLE status(id_status INT, status VARCHAR,
    PRIMARY KEY (id_status)
);

CREATE TABLE user (id_user INT, name VARCHAR, email VARCHAR, status_id INT,  
    CONSTRAINT un_user_email UNIQUE (email),
    PRIMARY KEY (id_user),
    FOREIGN KEY (status_id) REFERENCES status (id_status)
);
```

