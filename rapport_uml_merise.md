# Package de Modélisation UML + MERISE — CityHealth PFE

---

## 1. UML — Diagramme des Cas d'Utilisation

```plantuml
@startuml UseCaseDiagram
left to right direction
skinparam actorStyle awesome

actor "Utilisateur" as U
actor "Client (Citoyen)" as C
actor "Prestataire (Provider)" as P
actor "Développeur" as D
actor "Administrateur" as A

U <|-- C
U <|-- P
U <|-- D

rectangle "CityHealth Platform" {
  usecase "S'inscrire" as UC1
  usecase "Se connecter" as UC2
  usecase "Gérer son profil" as UC3

  usecase "Rechercher un provider" as UC4
  usecase "Prendre un rendez-vous" as UC5
  usecase "Consulter l'historique RDV" as UC6
  usecase "Gérer le carnet d'urgence" as UC7
  usecase "Participer au forum" as UC8
  usecase "Répondre urgence sang" as UC9
  usecase "Evaluer un provider" as UC10
  usecase "Publier une offre de don" as UC11

  usecase "Soumettre vérification" as UC12
  usecase "Gérer planning / RDV" as UC13
  usecase "Publier une annonce" as UC14
  usecase "Publier article (Research Hub)" as UC15
  usecase "Diffuser urgence sang" as UC16
  usecase "Gérer catalogue métier" as UC17

  usecase "Générer clé API" as UC18
  usecase "Consulter logs API" as UC19
  usecase "Accéder documentation API" as UC20

  usecase "Vérifier prestataires (OCR)" as UC21
  usecase "Modérer contenu" as UC22
  usecase "Gérer utilisateurs" as UC23
  usecase "Consulter analytics globales" as UC24
  usecase "Configurer la plateforme" as UC25
  usecase "Consulter journal d'audit" as UC26
}

U --> UC1
U --> UC2
U --> UC3

C --> UC4
C --> UC5
C --> UC6
C --> UC7
C --> UC8
C --> UC9
C --> UC10
C --> UC11

P --> UC12
P --> UC13
P --> UC14
P --> UC15
P --> UC16
P --> UC17

D --> UC18
D --> UC19
D --> UC20

A --> UC21
A --> UC22
A --> UC23
A --> UC24
A --> UC25
A --> UC26
@enduml
```

---

## 2. UML — Diagramme de Classes

```plantuml
@startuml ClassDiagram
skinparam classAttributeIconSize 0

abstract class Utilisateur {
  +id : UUID
  +email : String
  +motDePasse : String
  +role : Enum{client,provider,developer,admin}
  +dateInscription : DateTime
  +estActif : Boolean
  +seConnecter()
  +seDeconnecter()
  +modifierProfil()
}

class Client {
  +groupeSanguin : String
  +allergies : String[]
  +dateNaissance : Date
  +prendreRendezVous()
  +annulerRendezVous()
  +voirHistorique()
  +genererCarteUrgence()
}

class Provider {
  +nomEtablissement : String
  +typeProvider : String
  +ville : String
  +statutVerification : Enum{pending,verified,rejected}
  +planAbonnement : Enum{free,pro,enterprise}
  +notesMoyenne : Float
  +soumettreVerification()
  +gererPlanning()
  +publierArticle()
  +diffuserUrgenceSang()
}

class Developer {
  +nomApplication : String
  +planAPI : Enum{free,pro,enterprise}
  +quotaJournalier : Integer
  +genererCleAPI()
  +consulterLogs()
}

class Admin {
  +niveauAcces : Integer
  +approuverProvider()
  +rejeterProvider()
  +modererContenu()
  +consulterAudit()
}

class RendezVous {
  +id : UUID
  +dateHeure : DateTime
  +statut : Enum{pending,confirmed,completed,cancelled}
  +motif : String
  +notesProvider : String
  +confirmer()
  +annuler()
}

class Service {
  +id : UUID
  +nom : String
  +description : String
  +prix : Float
  +categorie : String
}

class CarteUrgence {
  +id : UUID
  +groupeSanguin : String
  +allergies : String[]
  +medicaments : String[]
  +contactUrgence : String
  +urlPublique : String
  +qrCode : String
}

class Annonce {
  +id : UUID
  +titre : String
  +description : String
  +type : Enum{annonce,publication}
  +statut : Enum{pending,approved,rejected,suspended}
  +estMiseEnAvant : Boolean
  +dateExpiration : DateTime
}

class ArticleRecherche {
  +id : UUID
  +titre : String
  +resume : String
  +contenu : String
  +categorie : String
  +doi : String
  +urlPdf : String
  +statut : Enum{pending,approved,rejected,suspended}
  +nbVues : Integer
}

class Notification {
  +id : UUID
  +type : String
  +message : String
  +estLue : Boolean
  +dateCreation : DateTime
  +marquerLue()
}

class Avis {
  +id : UUID
  +note : Integer
  +commentaire : String
  +statut : Enum{pending,approved,rejected}
  +dateCreation : DateTime
}

class CleAPI {
  +id : UUID
  +valeur : String
  +estActive : Boolean
  +quotaRestant : Integer
  +dateCreation : DateTime
  +regenerer()
  +desactiver()
}

class LogAPI {
  +id : UUID
  +methode : String
  +endpoint : String
  +statutHTTP : Integer
  +latenceMs : Integer
  +horodatage : DateTime
}

class UrgenceSang {
  +id : UUID
  +groupeSanguinRequis : String
  +niveauUrgence : String
  +statut : Enum{active,resolved}
  +message : String
  +dateCreation : DateTime
}

class AuditLog {
  +id : UUID
  +action : String
  +cible : String
  +details : JSON
  +horodatage : DateTime
}

Utilisateur <|-- Client
Utilisateur <|-- Provider
Utilisateur <|-- Developer
Utilisateur <|-- Admin

Client "1" --> "0..*" RendezVous : prend
Provider "1" --> "0..*" RendezVous : reçoit
Provider "1" --> "0..*" Service : propose
Provider "1" --> "0..*" Annonce : publie
Provider "1" --> "0..*" ArticleRecherche : rédige
Provider "1" --> "0..*" UrgenceSang : diffuse
Client "1" --> "0..1" CarteUrgence : possède
Client "1" --> "0..*" Avis : rédige
Provider "1" --> "0..*" Avis : reçoit
Developer "1" --> "0..*" CleAPI : gère
CleAPI "1" --> "0..*" LogAPI : génère
Utilisateur "1" --> "0..*" Notification : reçoit
Admin "1" --> "0..*" AuditLog : génère
@enduml
```

---

## 3. UML — Diagramme de Séquence : Authentification

```plantuml
@startuml SD_Auth
actor Utilisateur
participant "Frontend\n(React)" as FE
participant "Supabase Auth" as SA
database "PostgreSQL" as DB

Utilisateur -> FE : Saisit email + mot de passe
FE -> SA : supabase.auth.signInWithPassword({email, password})
SA -> DB : Vérifie credentials (auth.users)
DB --> SA : Utilisateur trouvé
SA -> DB : Lit user_roles
DB --> SA : Rôle retourné (client | provider | developer | admin)
SA --> FE : Session JWT + user object
FE -> FE : Stocke session (Zustand)
FE -> FE : Redirige vers dashboard selon le rôle
FE --> Utilisateur : Dashboard affiché
@enduml
```

---

## 4. UML — Diagramme de Séquence : Cycle de vie d'un rendez-vous

```plantuml
@startuml SD_RDV
actor Client
actor Provider
participant "Frontend" as FE
participant "appointmentService" as AS
database "Supabase DB" as DB
participant "notificationService" as NS

Client -> FE : Sélectionne créneau + provider
FE -> AS : createAppointment(providerId, date, motif)
AS -> DB : INSERT INTO appointments (statut='pending')
DB --> AS : RDV créé (id)
AS -> NS : sendBookingConfirmation(patientEmail)
NS --> Client : Email de confirmation

Provider -> FE : Consulte planning
FE -> AS : getAppointments(providerId)
AS -> DB : SELECT WHERE provider_id = X
DB --> FE : Liste des RDV en attente

Provider -> FE : Confirme le RDV
FE -> AS : updateAppointment(id, statut='confirmed')
AS -> DB : UPDATE appointments SET statut='confirmed'
DB --> AS : OK
AS -> NS : sendStatusUpdate(patientEmail, 'confirmed')
NS --> Client : Notification temps réel (Supabase Realtime)
@enduml
```

---

## 5. UML — Diagramme de Séquence : Vérification Provider (OCR)

```plantuml
@startuml SD_Verification
actor Provider
actor Admin
participant "Frontend" as FE
participant "ocrVerificationService" as OCR
participant "verificationService" as VS
database "Supabase DB" as DB
participant "Supabase Storage" as ST

Provider -> FE : Upload document (PDF/Image)
FE -> ST : storage.from('provider-images').upload(file)
ST --> FE : URL du document
FE -> OCR : verifyDocumentContent(file, expectedData)
OCR -> OCR : Tesseract.js OCR (FR+EN+AR)
OCR -> OCR : Levenshtein fuzzy match
OCR --> FE : VerificationResult {score, fields}
FE -> VS : submitVerification(providerId, docUrl, ocrScore)
VS -> DB : UPDATE providers SET verification_status='pending'
DB --> VS : OK

Admin -> FE : Consulte file vérification
FE -> DB : SELECT providers WHERE status='pending'
DB --> FE : Liste des providers
Admin -> FE : Approuve avec score OCR comme aide
FE -> VS : approveProvider(providerId)
VS -> DB : UPDATE providers SET verification_status='verified'
DB --> VS : OK
VS -> DB : INSERT INTO audit_logs (action='approved', target=providerId)
VS --> Provider : Notification "Profil vérifié ✓"
@enduml
```

---

## 6. UML — Diagramme d'Activité : Cycle de vie d'une Publication Médicale

```plantuml
@startuml AD_Publication
start
:Provider soumet une publication médicale;
:Vérification plan Premium;
if (Plan Premium ?) then (non)
  :Erreur NOT_PREMIUM;
  stop
else (oui)
  :Filtre de contenu (profanityFilter);
  if (Contenu approprié ?) then (non)
    :Erreur PROFANITY_DETECTED;
    stop
  else (oui)
    :INSERT INTO ads (statut='pending');
    :Notification Admin;
    :Admin examine la publication;
    if (Décision Admin ?) then (Approuver)
      :UPDATE statut='approved';
      :Publication visible publiquement;
    else if (Décision Admin ?) then (Rejeter)
      :UPDATE statut='rejected' + motif;
      :Notification Provider (rejet + motif);
    else (Suspendre)
      :UPDATE statut='suspended';
      :Notification Provider;
    endif
  endif
endif
stop
@enduml
```

---

## 7. MERISE — Modèle Conceptuel des Données (MCD)

```
UTILISATEUR (id_utilisateur, email, mot_de_passe, role, date_inscription, est_actif)

CLIENT (id_client, groupe_sanguin, allergies, date_naissance)
    hérite de UTILISATEUR

PROVIDER (id_provider, nom_etablissement, type_provider, ville, statut_verification, plan_abonnement, note_moyenne)
    hérite de UTILISATEUR

DEVELOPER (id_developer, nom_application, plan_api, quota_journalier)
    hérite de UTILISATEUR

ADMIN (id_admin, niveau_acces)
    hérite de UTILISATEUR

RENDEZ_VOUS (id_rdv, date_heure, statut, motif, notes_provider)
SERVICE (id_service, nom, description, prix, categorie)
CARTE_URGENCE (id_carte, groupe_sanguin, allergies, medicaments, contact_urgence, url_publique)
ANNONCE (id_annonce, titre, description, type, statut, est_mise_en_avant, date_expiration)
ARTICLE_RECHERCHE (id_article, titre, resume, contenu, categorie, doi, url_pdf, statut, nb_vues)
AVIS (id_avis, note, commentaire, statut, date_creation)
CLE_API (id_cle, valeur, est_active, quota_restant, date_creation)
LOG_API (id_log, methode, endpoint, statut_http, latence_ms, horodatage)
URGENCE_SANG (id_urgence, groupe_sanguin_requis, niveau_urgence, statut, message)
NOTIFICATION (id_notif, type, message, est_lue, date_creation)
AUDIT_LOG (id_audit, action, cible, details, horodatage)
POST_COMMUNAUTE (id_post, titre, contenu, categorie, est_anonyme, nb_upvotes)
COMMENTAIRE (id_commentaire, contenu, est_anonyme, nb_upvotes, id_parent)

──────────────────────────────────────────────────────
ASSOCIATIONS (Entité1, cardinalité, Association, cardinalité, Entité2)
──────────────────────────────────────────────────────
CLIENT       (1,1) — PREND         — (0,N) RENDEZ_VOUS
PROVIDER     (1,1) — REÇOIT        — (0,N) RENDEZ_VOUS
PROVIDER     (1,1) — PROPOSE       — (0,N) SERVICE
PROVIDER     (1,1) — PUBLIE        — (0,N) ANNONCE
PROVIDER     (1,1) — RÉDIGE        — (0,N) ARTICLE_RECHERCHE
PROVIDER     (1,1) — DIFFUSE       — (0,N) URGENCE_SANG
CLIENT       (1,1) — POSSÈDE       — (0,1) CARTE_URGENCE
CLIENT       (1,1) — RÉDIGE        — (0,N) AVIS
PROVIDER     (1,1) — REÇOIT        — (0,N) AVIS
DEVELOPER    (1,1) — GÈRE          — (0,N) CLE_API
CLE_API      (1,1) — GÉNÈRE        — (0,N) LOG_API
UTILISATEUR  (1,1) — REÇOIT        — (0,N) NOTIFICATION
ADMIN        (1,1) — GÉNÈRE        — (0,N) AUDIT_LOG
UTILISATEUR  (1,1) — PUBLIE        — (0,N) POST_COMMUNAUTE
POST_COMMUNAUTE (1,1) — CONTIENT   — (0,N) COMMENTAIRE
CLIENT       (0,N) — RÉPOND_À      — (1,1) URGENCE_SANG
```

---

## 8. MERISE — Modèle Logique des Données (MLD)

```sql
-- Table centralisée des utilisateurs (Supabase auth.users étendue)
UTILISATEUR(#id_utilisateur:UUID, email:VARCHAR, role:VARCHAR, date_inscription:TIMESTAMP, est_actif:BOOLEAN)

-- Profils spécialisés (extension de UTILISATEUR par FK)
CLIENT(#id_client:UUID => UTILISATEUR.id_utilisateur, groupe_sanguin:VARCHAR, allergies:TEXT, date_naissance:DATE)

PROVIDER(#id_provider:UUID => UTILISATEUR.id_utilisateur, nom_etablissement:VARCHAR, type_provider:VARCHAR, ville:VARCHAR, statut_verification:VARCHAR, plan_abonnement:VARCHAR, note_moyenne:FLOAT)

DEVELOPER(#id_developer:UUID => UTILISATEUR.id_utilisateur, nom_application:VARCHAR, plan_api:VARCHAR, quota_journalier:INTEGER)

ADMIN(#id_admin:UUID => UTILISATEUR.id_utilisateur, niveau_acces:INTEGER)

-- Entités métier
RENDEZ_VOUS(#id_rdv:UUID, date_heure:TIMESTAMP, statut:VARCHAR, motif:TEXT, notes_provider:TEXT, id_client:UUID => CLIENT.id_client, id_provider:UUID => PROVIDER.id_provider)

SERVICE(#id_service:UUID, nom:VARCHAR, description:TEXT, prix:DECIMAL, categorie:VARCHAR, id_provider:UUID => PROVIDER.id_provider)

CARTE_URGENCE(#id_carte:UUID, groupe_sanguin:VARCHAR, allergies:TEXT, medicaments:TEXT, contact_urgence:VARCHAR, url_publique:VARCHAR, id_client:UUID => CLIENT.id_client)

ANNONCE(#id_annonce:UUID, titre:VARCHAR, description:TEXT, type:VARCHAR, statut:VARCHAR, est_mise_en_avant:BOOLEAN, date_expiration:TIMESTAMP, id_provider:UUID => PROVIDER.id_provider)

ARTICLE_RECHERCHE(#id_article:UUID, titre:VARCHAR, resume:TEXT, contenu:TEXT, categorie:VARCHAR, doi:VARCHAR, url_pdf:VARCHAR, statut:VARCHAR, nb_vues:INTEGER, id_provider:UUID => PROVIDER.id_provider)

AVIS(#id_avis:UUID, note:INTEGER, commentaire:TEXT, statut:VARCHAR, date_creation:TIMESTAMP, id_client:UUID => CLIENT.id_client, id_provider:UUID => PROVIDER.id_provider)

CLE_API(#id_cle:UUID, valeur:VARCHAR, est_active:BOOLEAN, quota_restant:INTEGER, date_creation:TIMESTAMP, id_developer:UUID => DEVELOPER.id_developer)

LOG_API(#id_log:UUID, methode:VARCHAR, endpoint:VARCHAR, statut_http:INTEGER, latence_ms:INTEGER, horodatage:TIMESTAMP, id_cle:UUID => CLE_API.id_cle)

URGENCE_SANG(#id_urgence:UUID, groupe_sanguin_requis:VARCHAR, niveau_urgence:VARCHAR, statut:VARCHAR, message:TEXT, id_provider:UUID => PROVIDER.id_provider)

REPONSE_URGENCE(#id_reponse:UUID, id_urgence:UUID => URGENCE_SANG.id_urgence, id_client:UUID => CLIENT.id_client, statut:VARCHAR, date_reponse:TIMESTAMP)

NOTIFICATION(#id_notif:UUID, type:VARCHAR, message:TEXT, est_lue:BOOLEAN, date_creation:TIMESTAMP, id_utilisateur:UUID => UTILISATEUR.id_utilisateur)

AUDIT_LOG(#id_audit:UUID, action:VARCHAR, cible:VARCHAR, details:JSONB, horodatage:TIMESTAMP, id_admin:UUID => ADMIN.id_admin)

POST_COMMUNAUTE(#id_post:UUID, titre:VARCHAR, contenu:TEXT, categorie:VARCHAR, est_anonyme:BOOLEAN, nb_upvotes:INTEGER, id_utilisateur:UUID => UTILISATEUR.id_utilisateur)

COMMENTAIRE(#id_commentaire:UUID, contenu:TEXT, est_anonyme:BOOLEAN, nb_upvotes:INTEGER, id_post:UUID => POST_COMMUNAUTE.id_post, id_parent:UUID => COMMENTAIRE.id_commentaire, id_utilisateur:UUID => UTILISATEUR.id_utilisateur)
```

---

## 9. MERISE — Modèle Physique des Données (MPD / SQL)

```sql
-- MPD : Script SQL PostgreSQL prêt à l'implémentation

CREATE TABLE utilisateur (
    id_utilisateur UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email          VARCHAR(255) UNIQUE NOT NULL,
    role           VARCHAR(20) NOT NULL CHECK (role IN ('client','provider','developer','admin')),
    date_inscription TIMESTAMPTZ DEFAULT NOW(),
    est_actif      BOOLEAN DEFAULT TRUE
);

CREATE TABLE client (
    id_client      UUID PRIMARY KEY REFERENCES utilisateur(id_utilisateur) ON DELETE CASCADE,
    groupe_sanguin VARCHAR(5),
    allergies      TEXT,
    date_naissance DATE
);

CREATE TABLE provider (
    id_provider          UUID PRIMARY KEY REFERENCES utilisateur(id_utilisateur) ON DELETE CASCADE,
    nom_etablissement    VARCHAR(255) NOT NULL,
    type_provider        VARCHAR(50),
    ville                VARCHAR(100),
    statut_verification  VARCHAR(20) DEFAULT 'pending' CHECK (statut_verification IN ('pending','verified','rejected')),
    plan_abonnement      VARCHAR(20) DEFAULT 'free' CHECK (plan_abonnement IN ('free','pro','enterprise')),
    note_moyenne         FLOAT DEFAULT 0
);

CREATE TABLE developer (
    id_developer     UUID PRIMARY KEY REFERENCES utilisateur(id_utilisateur) ON DELETE CASCADE,
    nom_application  VARCHAR(255),
    plan_api         VARCHAR(20) DEFAULT 'free',
    quota_journalier INTEGER DEFAULT 500
);

CREATE TABLE admin (
    id_admin       UUID PRIMARY KEY REFERENCES utilisateur(id_utilisateur) ON DELETE CASCADE,
    niveau_acces   INTEGER DEFAULT 1
);

CREATE TABLE rendez_vous (
    id_rdv         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    date_heure     TIMESTAMPTZ NOT NULL,
    statut         VARCHAR(20) DEFAULT 'pending' CHECK (statut IN ('pending','confirmed','completed','cancelled')),
    motif          TEXT,
    notes_provider TEXT,
    id_client      UUID NOT NULL REFERENCES client(id_client),
    id_provider    UUID NOT NULL REFERENCES provider(id_provider)
);

CREATE TABLE service (
    id_service  UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    nom         VARCHAR(255) NOT NULL,
    description TEXT,
    prix        DECIMAL(10,2),
    categorie   VARCHAR(100),
    id_provider UUID NOT NULL REFERENCES provider(id_provider)
);

CREATE TABLE carte_urgence (
    id_carte        UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    groupe_sanguin  VARCHAR(5),
    allergies       TEXT,
    medicaments     TEXT,
    contact_urgence VARCHAR(255),
    url_publique    VARCHAR(512) UNIQUE,
    id_client       UUID UNIQUE NOT NULL REFERENCES client(id_client)
);

CREATE TABLE annonce (
    id_annonce        UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    titre             VARCHAR(255) NOT NULL,
    description       TEXT,
    type              VARCHAR(20) CHECK (type IN ('annonce','publication')),
    statut            VARCHAR(20) DEFAULT 'pending' CHECK (statut IN ('pending','approved','rejected','suspended')),
    est_mise_en_avant BOOLEAN DEFAULT FALSE,
    date_expiration   TIMESTAMPTZ,
    id_provider       UUID NOT NULL REFERENCES provider(id_provider)
);

CREATE TABLE article_recherche (
    id_article  UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    titre       VARCHAR(255) NOT NULL,
    resume      TEXT,
    contenu     TEXT,
    categorie   VARCHAR(100),
    doi         VARCHAR(255),
    url_pdf     TEXT,
    statut      VARCHAR(20) DEFAULT 'pending',
    nb_vues     INTEGER DEFAULT 0,
    id_provider UUID NOT NULL REFERENCES provider(id_provider)
);

CREATE TABLE avis (
    id_avis       UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    note          INTEGER CHECK (note BETWEEN 1 AND 5),
    commentaire   TEXT,
    statut        VARCHAR(20) DEFAULT 'pending',
    date_creation TIMESTAMPTZ DEFAULT NOW(),
    id_client     UUID NOT NULL REFERENCES client(id_client),
    id_provider   UUID NOT NULL REFERENCES provider(id_provider)
);

CREATE TABLE cle_api (
    id_cle         UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    valeur         VARCHAR(512) UNIQUE NOT NULL,
    est_active     BOOLEAN DEFAULT TRUE,
    quota_restant  INTEGER DEFAULT 500,
    date_creation  TIMESTAMPTZ DEFAULT NOW(),
    id_developer   UUID NOT NULL REFERENCES developer(id_developer)
);

CREATE TABLE log_api (
    id_log      UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    methode     VARCHAR(10),
    endpoint    VARCHAR(255),
    statut_http INTEGER,
    latence_ms  INTEGER,
    horodatage  TIMESTAMPTZ DEFAULT NOW(),
    id_cle      UUID REFERENCES cle_api(id_cle)
);

CREATE TABLE urgence_sang (
    id_urgence           UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    groupe_sanguin_requis VARCHAR(5),
    niveau_urgence       VARCHAR(20),
    statut               VARCHAR(20) DEFAULT 'active',
    message              TEXT,
    date_creation        TIMESTAMPTZ DEFAULT NOW(),
    id_provider          UUID NOT NULL REFERENCES provider(id_provider)
);

CREATE TABLE reponse_urgence (
    id_reponse   UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    statut       VARCHAR(20) DEFAULT 'pending',
    date_reponse TIMESTAMPTZ DEFAULT NOW(),
    id_urgence   UUID NOT NULL REFERENCES urgence_sang(id_urgence),
    id_client    UUID NOT NULL REFERENCES client(id_client)
);

CREATE TABLE notification (
    id_notif       UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    type           VARCHAR(50),
    message        TEXT,
    est_lue        BOOLEAN DEFAULT FALSE,
    date_creation  TIMESTAMPTZ DEFAULT NOW(),
    id_utilisateur UUID NOT NULL REFERENCES utilisateur(id_utilisateur)
);

CREATE TABLE audit_log (
    id_audit   UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    action     VARCHAR(100),
    cible      VARCHAR(255),
    details    JSONB,
    horodatage TIMESTAMPTZ DEFAULT NOW(),
    id_admin   UUID NOT NULL REFERENCES admin(id_admin)
);

CREATE TABLE post_communaute (
    id_post        UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    titre          VARCHAR(255),
    contenu        TEXT,
    categorie      VARCHAR(50),
    est_anonyme    BOOLEAN DEFAULT FALSE,
    nb_upvotes     INTEGER DEFAULT 0,
    id_utilisateur UUID NOT NULL REFERENCES utilisateur(id_utilisateur)
);

CREATE TABLE commentaire (
    id_commentaire UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    contenu        TEXT,
    est_anonyme    BOOLEAN DEFAULT FALSE,
    nb_upvotes     INTEGER DEFAULT 0,
    id_post        UUID NOT NULL REFERENCES post_communaute(id_post) ON DELETE CASCADE,
    id_parent      UUID REFERENCES commentaire(id_commentaire),
    id_utilisateur UUID NOT NULL REFERENCES utilisateur(id_utilisateur)
);

-- Indexes pour les performances
CREATE INDEX idx_rdv_client    ON rendez_vous(id_client);
CREATE INDEX idx_rdv_provider  ON rendez_vous(id_provider);
CREATE INDEX idx_avis_provider ON avis(id_provider);
CREATE INDEX idx_log_api_cle   ON log_api(id_cle);
CREATE INDEX idx_annonce_prov  ON annonce(id_provider);
CREATE INDEX idx_notif_user    ON notification(id_utilisateur);
```
