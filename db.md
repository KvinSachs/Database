#Base de Données

* Définition de données
* Manipulation de données
* Contrôle de données

*DBA* Database Administrator

##Instructions SQL

####Create Table

Sur Unix -> noms de table et noms de base sont sensibles à la casse mais pas les noms de colonnes ou de contraintes ne le sont pas (de base).  

* CREATE [TEMPORARY] TABLE database.table
* (colonne1 type1 [NOT NULL | NULL] [DEFAULT valeur1] [COMMENT "commentaire"], colonne2 type2, ...)
* [CONSTRAINT nomContrainte1 typeContrainte1, ...]
* [ENGINE= InnoDB | MyISAM | ...]

#####Contrainte

* CONSTRAINT un_table_champ
* UNIQUE (colonne1)

* CONSTRAINT PRIMARY KEY (id_user)  


Example :  
```
CREATE TABLE user  
(id_user INT, name VARCHAR, email VARCHAR),  
CONSTRAINT un_user_email UNIQUE (email),
PRIMARY KEY (id_user);
```

