### ITZBund Rancher Fleet Demo

Demo für die Verwendung von [Fleet](https://fleet.rancher.io/) in Rancher: https://rancher.backstage-demo-itzbund.de, Login: guest/guestpassword.

Dieses Repository basiert auf dem [Kubernetes Guestbook Beispiel](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/) und wird als "Git Repo" in Rancher importiert, siehe https://rancher.backstage-demo-itzbund.de/dashboard/c/_/fleet/fleet.cattle.io.gitrepo:

<img width="1076" height="243" alt="image" src="https://github.com/user-attachments/assets/ac5a0c86-3264-495d-b03b-65aba6d2d49e" />

In Rancher werden sechs Kubernetes-Cluster verwaltet:
-	der lokale Cluster für den Rancher-Server selbst (K3s-Cluster, der auf einer EC2-Instanz ausgeführt wird)
-	ein bestehender Kubernetes-Cluster, der in Rancher importiert wurde (azure-001)
-	ein bestehender EKS-Cluster, der in Rancher importiert wurde (eks-001)
-	ein von Rancher erstellter EKS-Cluster (eks-002)
-	ein von Rancher auf einer bestehenden EC2-Instanz erstellter RKE2-Cluster (rancher-001)
-	ein von Rancher auf von Rancher erstellten EC2-Instanzen erstellter RKE2-Cluster (rancher-002)

Persistenz:
- Auf eks-001 und eks-002 wurde der Out of tree EBS CSI Driver aktiviert und eine entsprechende Storage Class ebs (als Default Storage Class) angelegt.
- Auf rancher-001 und rancher-002 wurde Longhorn installiert. Dabei wurde automatisch eine entsprechende Storage Class longhorn (als Default Storage Class) angelegt.

Netzwerk:
- Auf eks-001 und eks-002 wurde der NGINX Ingress Controller eingespielt.

Die Guestbook-Applikation wird von Fleet automatisch auf den fünf Clustern azure-001 (env=dev), eks-001 und eks-002 (env=test) und rancher-001 und rancher-002 (env=prod) eingespielt. Dabei werden die Ressourcen automatisch angepasst:
- Auf azure-001 (env=dev) werden keine Redis Follower eingespielt
- Auf eks-001 und eks-002 (env=test) wird das Frontend Deployment auf 2 Replicas hochskaliert
- Auf rancher-001 und rancher-002 (env=prod) wird das Frontend Deployment auf 3 Replicas hochskaliert
- Auf eks-001 und eks-002 (env=test) und auf rancher-001 und rancher-002 (env=prod) wird ein Ingress eingespielt, über den die Applikation von außen aufgerufen werden kann (DNS-Einträge in AWS Route 53):
  - http://guestbook.eks-001.backstage-demo-itzbund.de/ - Zugriff über einen AWS Load Balancer
  - http://guestbook.eks-002.backstage-demo-itzbund.de/ - Zugriff über einen AWS Load Balancer
  - http://guestbook.rancher-001.backstage-demo-itzbund.de/
  - http://guestbook.rancher-002.backstage-demo-itzbund.de/
- Auf eks-001 und eks-002 (env=test) und auf rancher-001 und rancher-002 (env=prod) wird das Redis Leader Deployment so geändert, dass der Redis-Server alle 60 Sekunden (bei Änderungen) den Datenbestand in den Ordner /data schreibt. Bei dem Ordner handelt es sich um ein gemountetes Volume, welches über ein Persistent Volume Claim in der Default Storage Class angelegt wird.
  - Auf eks-001 und eks-002 (Storage Class ebs) wird automatisch ein AWS Elastic Block Store (EBS) Volume angelegt.
  - Auf rancher-001 und rancher-002 (Storage Class longhorn) wird automatisch in Longhorn ein Volume angelegt.

<img width="984" height="545" alt="image" src="https://github.com/user-attachments/assets/950a04f3-e7a6-4fb7-9a22-b2b9637cc7b3" />

Das Deployment erzeugt ein Bundle, in dem alle zu der Applikation gehörenden Ressourcen über die beiden Cluster hinweg verwaltet werden können:

<img width="985" height="758" alt="image" src="https://github.com/user-attachments/assets/12e72d37-a470-4246-b1a3-7f8b90f7df4a" />

Wenn man in diesem Repo im Branch main eine Änderung vornimmt, wird diese automatisch auf den betroffenen Clustern eingespielt.
