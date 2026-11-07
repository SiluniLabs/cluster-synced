# Keycloak

Keycloak is deployed as a single stateless pod. Realm and user data are stored
in the external PostgreSQL database; the pod has no persistent volume.

Before Argo CD syncs this application:

1. Make `account.siluni.fr` resolve to the cluster's Traefik ingress.
2. Edit the encrypted database and initial-admin values with
   `sops apps/keycloak/secret.sops.yaml`. Set a PostgreSQL host reachable from
   the cluster, database, username, password, and a strong initial admin
   password. Keep the file encrypted when committing it.
3. The `db-ca.yaml` ConfigMap contains the AWS RDS global CA bundle, mounted
   into Keycloak for PostgreSQL TLS verification. The JDBC URL properties use
   `sslmode=verify-full`, which verifies both the certificate chain and that
   the PostgreSQL certificate matches the hostname configured as `DB_HOST`.

The bootstrap admin credentials are only used when Keycloak initializes its
first administrator. After setup, rotate the administrator credentials in
Keycloak, then remove the two bootstrap environment entries from
`deployment.yaml` and their values from the encrypted Secret before committing.
