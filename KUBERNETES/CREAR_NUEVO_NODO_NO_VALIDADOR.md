Pasos crear nuevo nodo (no validador)

#Crear nueva llave:
besu --data-path=/home/oscar-214/Documents/BESU/besu-25.9.0/bin/networkFiles/keys/Node-5-KEY \
  public-key export \
  --to=/home/oscar-214/Documents/BESU/besu-25.9.0/bin/networkFiles/keys/Node-5-KEY/key.pub

#Crear secreto de la llave:
kubectl create secret generic besu-node-key-4 -n tfg --from-file=key=/home/oscar-214/Documents/BESU/besu-25.9.0/bin/IBFT-NW/keys/Node-5-KEY/key

#Almacenar las llaves creadas dentro de una nueva carpeta data para el Node-5 para el nuevo volumen

#En StatefulSet.yaml:
#Antes:
replicas: 4

#Después:
replicas: 5

#añadir nueva carpeta data: 
- name: node-key-4
  mountPath: /etc/besu/node-key-4
  readOnly: true

#modificar:
# Antes:
else
  cp /etc/besu/node-key-3/key /opt/besu/data/key
fi

# Después:
elif [ "$(hostname)" = "besu-3" ]; then
  cp /etc/besu/node-key-3/key /opt/besu/data/key
else
  cp /etc/besu/node-key-4/key /opt/besu/data/key
fi

#añadir nuevo volumen:
- name: node-key-4
  secret:
    secretName: besu-node-key-4

#En RPC_Service.yaml:
#añadir:
---
apiVersion: v1
kind: Service
metadata:
  name: besu-4-rpc
  namespace: tfg
spec:
  selector:
    statefulset.kubernetes.io/pod-name: besu-4
  ports:
  - name: http
    port: 8549
    targetPort: 8545
    protocol: TCP
