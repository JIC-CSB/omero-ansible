Upgrading the OMERO server software
===================================

1. (if production) arrange a time to snapshot of VM via main Computing (about a week beforehand).

2. Update variables in provisioning/roles/updatedb/default/main.yml

3. Update variables in provisioning/vars.yml
   - omero_version
   - omero_branch
   - omero_underscore_version
   - omero_schema_sql

4. Check OMERO update notes and edit provisioning/omero_update.yml to update postgres/python/nginx/etc. accordingly.

5.
   - (if production) request snapshot
   - make sure backups have been working

6. Shutdown services

   systemctl stop nginx
   systemctl stop omero-web
   systemctl stop omero-admin

7. Run provisioning/omero_update.yml playbook to
   - backup the current database
   - update dependencies e.g. postgres/python/nginx/etc.
   - create the SQL migration patch
   - apply the SQL patch

   ansible-playbook -i provisioning/hosts provisioning/omero_update.yml --limit [dev|prod|local]

8. Resume services

   systemctl start omero-admin
   systemctl start omero-web
   systemctl start nginx

9. Check web ui to see if data is present and correct
