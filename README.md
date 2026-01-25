### ITZBund Rancher Fleet Demo

Demo für die Verwendung von [Fleet](https://fleet.rancher.io/) in Rancher: https://rancher.backstage-demo-itzbund.de, Login: guest/guestpassword.

Dieses Repository basiert auf dem [Kubernetes Guestbook Beispiel](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/) und wird als "Git Repo" in Rancher importiert, siehe https://rancher.backstage-demo-itzbund.de/dashboard/c/_/fleet/fleet.cattle.io.gitrepo:

<img width="1076" height="243" alt="image" src="https://github.com/user-attachments/assets/ac5a0c86-3264-495d-b03b-65aba6d2d49e" />

Die Guestbook-Applikation wird von Fleet automatisch auf den beiden Clustern amazon-001 (env=dev) und rancher-001 (env=prod) eingespielt. Dabei werden die Ressourcen automatisch angepasst:
- Auf amazon-001 (env=dev) werden keine Redis Follower eingespielt
- Auf rancher-001 (env=prod) wird das Frontend Deployment auf 3 Replicas hochskaliert, und es wird ein Ingress eingespielt, über den die Applikation von außen aufgerufen werden kann: http://guestbook.backstage-demo-itzbund.de/

<img width="984" height="545" alt="image" src="https://github.com/user-attachments/assets/950a04f3-e7a6-4fb7-9a22-b2b9637cc7b3" />

Das Deployment erzeugt ein Bundle, in dem alle zu der Applikation gehörenden Ressourcen über die beiden Cluster hinweg verwaltet werden können:

<img width="985" height="758" alt="image" src="https://github.com/user-attachments/assets/12e72d37-a470-4246-b1a3-7f8b90f7df4a" />

Wenn man in diesem Repo im Branch main eine Änderung vornimmt, wird diese automatisch auf amazon-001 und/oder rancher-001 eingespielt.
