# Keycloak

Keycloak is deployed as a single stateless pod. Realm and user data are stored
in the external PostgreSQL database; the pod has no persistent volume.

Before Argo CD syncs this application:

1. Make `account.siluni.fr` resolve to the cluster's Traefik ingress.
2. Edit the encrypted database and initial-admin values with
   `sops apps/keycloak/secret.sops.yaml`. Set a PostgreSQL host reachable from
   the cluster, database, username, password, and a strong initial admin
   password. Keep the file encrypted when committing it.
3. Ensure PostgreSQL presents a certificate trusted by the Keycloak container
   and valid for its hostname; the JDBC connection uses `sslmode=verify-full`.

The bootstrap admin credentials are only used when Keycloak initializes its
first administrator. After setup, rotate the administrator credentials in
Keycloak, then remove the two bootstrap environment entries from
`deployment.yaml` and their values from the encrypted Secret before committing.
