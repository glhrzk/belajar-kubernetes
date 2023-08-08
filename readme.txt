Kubernetes master:
1. kube-apiserver bertugas sebagai API yang digunakan untuk berinteraksi/berkomunikasi dengan Kubernetes Cluster
2. etcd bertugas sebagai database untuk menyimpan data Kubenertes Cluster
3. kube-scheduler bertugas untuk memperhatikan aplikasi yang kita jalankan dan meminta node untuk menjalankan aplikasi di cluster
5. kube-controller-manager bertugas melakukan kontrol terhadap kubernetes cluster
6. cloud-controller-manager bertugas melakukan kontrol terhadap interaksi dengan cloud provider

Kubernetes Nodes: 
1. kubelet berjalan di setiap node dan bertugas memastikan bahwa aplikasi kita berjalan di Node
2. kube-proxy berlajan disetiap node dan bertugas sebagai proxy terhadap arus network yang masuk ke aplikasi kita dan bisa bertindak sebagai load balancer
3. container-manager berjalan di setiap node dan bertugas sebagai container manager. Kubernetes mendukung beberapa container manager seperti Docker, containerd, cri-o, rktlet, dll.

Master: 
digunakan untuk mengelola/manage node.

Node:
node adalah worker machine di kubernetes, node bisa saja dalam bentuk VM atau bentuk fisik.
di dalam node selalu terdapat kubelete, kube-proxy dan container manager.

===========================================================================================
perintah / command prompt kubectl
1. kubectl get nodes [untuk melihat semua node yang ada]
2. kubectl describe node namaNode [untuk melihat detail node yang dituju]
3. kubectl get pod [untuk melihat semua pod yang ada]
4. kubectl get pod -o wide [untuk melihat semua pod yang ada dengan detail]
5. kubectl describe pod namapod [untuk melihat detail pod yang dituju]
6. kubectl create -f namafilepod.yaml [untuk membuat pod/namespace/rc/rs/daemonset]
7. kubectl port-forward namapod portAkses:portPod [untuk sekedar mengetes apakah pod yang kita buat jalan atau tidak sifatnya semetara]
8. kubectl get pod --show-labels [untuk melihat semua pod dan labelnya]
9. kubectl label pod namapod key="value" [untuk menambahkan label secara manual, gunakan parameter --rewrite untuk mereplace]
10. kubectl annotate pod namapod key="value" [untuk menambahkan annotation secara manual, gunakan parameter --rewrite untuk mereplace]
11. kubectl get namespace/s/ns [untuk melihat semua namespace yang ada]
12. kubectl get pod --namespace namanamespace [untuk melihat semua pod di namespace yang dituju, gunakana parameter -n untuk menyingkat]
13. kubectl create -f namafile.yaml --namespace namanamespace [untuk membuat pod dengan namespace spesific, dengan catatan namespace tersebut harus sudah ada/dibuat]
14. kubectl delete namespace namanamespace [jika namespace dihapus, maka semua pod yang menggunakan namespace tersebut akan dihapus juga]
15. kubectl delete pod namapod1 namapod2 namapod3 [untuk menghapus pod] 
16. kubectl delete pod --all --namespace namanamespace [menghapus semua pod berdasarkan namespace]
17. kubectl get replicationcontroller [untuk melihat semua replicacontroller, bisa juga menggunakan kubectl get rc ]
18. kubectl delete replicationcontroller namarc [untuk menghapus rc, jika rc dihapus maka semua pod yang didalamnya juga terhapus, jika pod yang didalamnya tidak terhapus gunakan parameter --cascade=false ]
19. kubectl get replicaset [untuk menampilkan semua rs yang ada]
20. kubectl label node namanode key=value
21. kubectl get all
21. kubectl get all --namespace namanamespace
22. kubectl delete all --all
23. kubectl delete all --all --namespace namanamespace
25. kubectl exec nama-pod -- env [melihat env di pod]
26. kubectl get endpoints nama-service
27. minikube service nginx-service [untuk melihat node ip yang di exposes]
28. kubectl apply -f deployment.yaml (membuat deployment)
29. kubectl get deployments (melihat semua deployment)
30. kubectl delete deployment namadeployment (menghapus deployment)
31. kubectl describe deployment namadeployment (melihat deployment secara detail)
===========================================================================================

Pod:
Pod adalah unit terkecil yang bisa di deploy ke Kubernetes cluster, Pod berisi satu atau lebih dari container.
secara sederhana pod adalah aplikasi yang kita running di Kubernetes cluster.
dalam 1pod pasti di running dalam 1node, tidak bisa di partial/pisah.

Label:
untuk memberi tanda pada pod, mengorganisir pod.
memberi informasi tambahan pada pod, label tidak boleh terlalu panjang dan tidak boleh ada spasi.
label tidak hanya digunakan pada pod, tapi pada semua resource di kubernetes; seperti replication controller, replica set, service dll.
label juga bisa digunakan untuk query pencarian, delete, replication controller
    perintah: 
    1. kubectl get pods -l key
    2. kubectl get pods -l key=value
    3. kubectl get pods -l '!key' [selain key]
    4. kubectl get pods -l key!=value [key tidak sama dengan value]
    5. kubectl get pods -l 'key in (value1, value2)' [sama dengan nilai array]
    6. kubectl get pods -l 'key notin (value1, value2)' [tidak sama dengan nilai array]
    7. kubectl delete pods -l key=value [menhapus pod dengan label]

Annotaion:
sama seperti label yaitu menambahkan data tambahan, akan tetapi annotation tidak bisa digunakan di query pencarian.
annotation mendukung penambahan data dalam jumlah/ukuran besar, yaitu bisa menampung data hingga 256kb

Namespace:
digunakan ketika resource di Kubernetes sudah terlalu banyak.
ketika butuh memisahkan resource untuk multi-tenan,team atau environment.
nama resources/pod bisa sama jika menempatkan di namespace yang berbeda.
default, adalah namespace yang secara otomatis digunakan ketika kita membuat pod.
namespace bukanlah cara untuk mengisolasi resource.
walaupun pod menggunakan namespace yang berbeda pod akan tetap bisa saling berkomunikasi dengan pod lain.

Probe:
Liveness Probe: melakukan pengecekan, jika respon dari pengecekan tidak sehat/unhealthy maka akan merestart pod
Readiness Probe: melakukan pengecekan, jika respon dari pengecekan tidak sehat/unhealthy maka akan menghentikan/stop rx traffic
Startup Probe: melakukan pengecekan, yang dilakukan hanya diawal; cocok digunakan untuk aplikasi yang startupnya lama seperti mongo express
    detail pengecekan probe:
    1. initialDelaySecond, waktu counter/pengecekan setelah container/pod dibuat.
    2. periodSecond, berapa lama waktu setiap kali pengecekan
    3. timeoutSecond, berapa lama waktu gagal setiap kali pengecekan
    4. successTreshold, minimum dianggap sukses setelah berstatus failure; default 1
    5. failurTreshold, minimum dianggap gagal; default 3 

    mekanisme pengecekan Probe
    1. HTTP Get
    2. TCP Socket
    3. Command Exec

Replication Controller:
replication controller bertugas untuk memastikan bahwa pod selalu berjalan.
jika salah satu pod mati atau hilang (terjadi jika nodenya mati), maka replication controller secara otomatis akan membuat/menjalankan pod yang hilang tersebut, replication contoller biasanya ditugaskan memanage lebih dari 1 pod.
replication controller akan memastikan jumlah pod yang berjalan sudah sesuai dengan yang di tentukan; jika kurang akan menambah/membuat pod baru, jika lebih akan menghapus pod tersebut.
    menghapus replication controller:
    1. kubectl delete replicationcontroller 

Replication Set:
hampir sama dengan replication set, hanya saja replication set memiliki label selector yang lebih expressive/match expression.

Operasi-operasi match Expresion:
In, value label harus ada di in
NotIn, value label tidak boleh ada di value in
Exist, label harus ada
NotExist, label tidak boleh ada

Daemon set:
pada saat membuat pod menggunakan rc/rs pada defaultnya kubernetes akan memilih secara random node yang dipilih.
jika kita ingin membuat di node mana pod akan dibuat atau akan menginstall ke semua node (contoh 1 pod/aplikasi per node), kita bisa mengaturnya secara manual dengan daemon set.
 contoh kasus:
 1. aplikasi untuk monitoring node
 2. aplikasi untuk mengambil lode node

Job:
job adalah resource di kubernetes yang digunakan untuk menjalan pod hanya sekali.
berbeda dengan rc, rs, dan daemonset yang akan membuat secara otomatis ketika pod tersebut hilang/rusak.
job akan mati jika pekerjaannya akan selesai.
    contoh kasus:
    1. backup atau restore database
    2. import atay expore data
    3. aplikasi batch

Cronjob:
cronjob di k8s menggunakan cronjob linux pada umumnya, berbeda dengan job, cronjob menggunakan JobTemplate.

Nodeselector:
jika kita tidak mengatur node mana yang akan di pilih ketika mendeploy maka k8s akan secara otomatis memilih node secara random.
kita juga bisa mengatur jika ingin menentukan di node mana pod akan dibuat dengan memambahkan label pada nod.
contoh kasus node yang dituju memiliki spesifikasi yang mumpuni seperti menggunakan GPU Dedicated, dan SSD.

Get All:
bisa digunakan untuk melihat semua resource (rs, ds, rc, cr, job, service)
bisa juga digunakan untuk menghapus.
kita juga bisa menambahkan parameter

=============================[ SERVICE SERIES]=============================
Service:
service adalah resource di k8s yang digunakan untuk membuat satu gerbang untuk mengakses satu atau lebih Pod.
service memiliki ip address dan port yang tidak pernah berubah selama service itu ada.
client bisa mengakses service tersebut, dan secara otomatis akan meneruskan ke pod yang ada dibelakang service tersebut.
dengan begini client/host tidak perlu tahu lokasi tiap pod, dan pod bisa bertambah berkurang, atau berpindah tanpa harus mengganggu client.
ip atau port yang ada di service hanya bisa di akses oleh pod.

jika pod lain ingin mengakses (pod lain) menggunakan service secara langsung tanpa harus mengetahui ip service, kita bisa menggunakan ENV variable atau DNS 
  dengan syntax: nama-service.nama-namespace.svc.cluster.local , dengan catatan masih di dalam service yang sama.

Endpoint:
terkadang pod(aplikasi) membutuhkan akses ke aplikasi luar contoh payment gateway dan ekspedisi, external service atau endpoint bisa mengatasi itu, dibandingkan setiap pod mengaksesnya secara langsung kita bisa membuat endpoint yang menghandle itu semua.
secara singkat ini adalah mengakses link/url di luar service (transmit/get data).

Eksposes Service: 
contoh: client/mobile/internet yang mengaksesnya.

Tipe-tipe Service:
- ClusterIp: mengekspos service di dalam internal kubernetes cluster (default)
- ExternalName: memetakan service ke externalname (misalnya: example.com)
- NodePort: mengekspos service pada setiap ip node dan port yang sama, kita bisa mengakses service dengant tipe ini, dari luar cluster melalui <nodeip>:<node:port>
- LoadBalancer: mengeskpos service secara external dengan menggunakan loadbalanceer yang disediakan oleh penyedia layanan cloud.

cara untuk ekspos service:
-> dengan menggunakan nodePort, node akan membuka port yang akan meneruskan request ke service yang dituju.
   dengan menambahkan type: NodePort dan nantinya akan dibuat port otomatis atau bisa juga kita tentukan. 
   kekurangan:  harus mengekspos semua node yang ada, dan juga harus menghapal node dan ip yang ada bayangkan jika ada beberapa node maka akan sangat merepotkan.
   
-> dengan menggunakan loadbalancer, service bisa di akses via lb dan akan meneruskan request ke node port dan di lanjutkan ke service.
   dengan menambahkan type: LoadBalancer dan nantinya akan dibuat port otomatis.
   kekurangan:  harus mengekspos semua node yang ada, dan juga harus menghapal node dan ip yang ada.
    
-> menggunakan ingress, dimana ingress adalah resource yang memang ditujukan untuk mengekspos service. kekurangan inggress hanya beroperasi di level HTTP.
    
=============================[ SERVICE SERIES]=============================



=============================[ VOLUME SERIES]=============================
VOLUME:
berkas/file/data di dalam container sifatnya tidak permanen, akan terhapus seiring dihapusnya pod atau container ataupun merestart, volume secara sederhana adalah sebuah directori yang bisa di akses oleh contianer-container di Pod.
jenis-jenis volume:
emptyDir: direktori sederhana (kosong) | tidak cocok digunakan di Prod
hostPath: digunakan untuk menghasring direktori di node ke pod
gitRepo: directory yang dibuat pertama kali dengan mengclone git repo
nfs: sharing network file system.
dll.

Sharing Volume:
karena didalam pod kita bisa membuat lebih dari satu container, maka volume di pod pun bisa kita sharing ke beberapa container.
contoh, container pertama akan membuat file, container kedua akan memproses file.
=============================[ VOLUME SERIES]=============================

Environment Variable:
environment variable di kubernetes sama/tidak beda jauh dengan environment di kontainer, yaitu agar dynamis tidak di hardcode.


Config Map:
digunakan untuk menyimpan konfigurasi aplikasi, seperti variabel lingkungan dan file konfigurasi, sebagai pasangan kunci-nilai. 


Secret:
sama seperti configmap tetapi dengan data yang sifatnya sensitive, seperti username password database, API key, Secret key dsj.
data: (berarti memasukan data dalam bentuk bas64)
stringData:(berarti memasukan dalam bentuk plainText)


Downrade API:
downward api memungkinankan kita mengambil informasi seputar pod dan node maelalui env variable.
list metatada
request.cpu = jumlah cpu yang di request
request.memory = jumlah memory yang di request
limits.cpu = jumlah limit maksimal cpu
limits.memory = jumlah limit maksimal memory
metadata.name = nama pod
metadata.namespace = namespace pod
metadata.uid = id pod
metadata.labels['<key>'] = label pod
metadata.annotations['<key>'] = annotation pod
status.podIP = ip address pod
status.hostIP = IP address node
spec.serviceAccountName = nama service account pod
spec.nodeName = nama node


Imperative Management:
kubectl create -f namafile.yaml (membuat kubernetes object yang ada dalam file tersebut)
kubectl replace -f namafile.yaml (mengupdate kubernetes object yang ada dalam file tersebut *tidak semua bisa di update)
kubectl get -f namafile.yaml -o yaml/json (melihat kubernetes object yang ada dalam file tersebut)
kubectl delete -f namafile.yaml (menghapus kubernetes object yang ada dalam file tersebut)

Declarative Management:
kubectl apply -f namafile.yaml (membuat/mengupdate kubernetes object yang ada dalam file tersebut)
kelebihan:
- saat menggunakan declarative management, file konfigurasi/yaml akan disimpan didalam annotations object, ini berguna untuk rollback version.
- hal ini sangat bermanfaat saat menggunakan object Deployment

Deployment:
pada nantinya kita tidak akan membuat pod/replicaset secara manual untuk aplikasi kita, melaikan menggunakan deployment, dibelakang deployment adalah replicaset, seolah olah deployment adalah yang mengontrol replicaset, penamaan pod pun akan mengikuti replicaset yang dibuat oleh deployment.

Update Deployment:
cara kerja update pada deployment, deployment akan membuat replicaset baru dan menunggu pod yang dibuat dari replicaset tersebut ready dan running, ketika pod yang baru sudah terbuat maka pod yang lama dari replicaset sebelumnya akan di terminate/delete. dan replicaset yang lama tidak akan dihapus.
pada defaultnya replicaset yang akan disimpan maksimal 10.

Rollback Deployment:
di k8s rollback deployment bisa meng apply file konfigurasi yang digunakan sebelumnya, tetapi itu cara manual. ada cara otomatisnya menggunakan kubernetes rollout.
k8s rollout command:
kubectl rollout history object name (melihat history rollout)
kubectl rollout pause object name (menandai sebagai pause)
kubectl rollout resume object name (resume pause)
kubectl rollout restart object name (merestart rollout)
kubectl rollout status object name (melihat status rollout)
kubectl rollout undo object name (undo ke rollout sebelumnya)
catatan, object diatas deployments/daemonsets/statefullset


Persistant Volume: 
sama dengan volume biasa, tetapi dengan cara kerja dan cara pembuatan yang berbeda.
tahapan PV:
  - membuat persistant volume
  - membuat persistant volume Claim
  - menambahkan persistant volume claim ke pod
kelebihan, bisa memberikan garansi size yang akan ditentukan untuk pod tujuan (yang mengklaim).
Persistent volume Command:
kubectl get pv
kubectl describe pv namapv
kubectl detele pv namapv
kubectl get pvc
kubectl descirve pvc namapvc 
kubectl detele pvc namapvc


StateFullSet:
pod, rs, rc, deployment adalah object yang cocok digunakan untuk aplikasi jenis stateless, stateless artinya aplikasi yang tidak menyimpan state/data.
lalu bagaimana dengan aplikasi yang harus menyimpan data(statefull) seperti database yang tidak boleh sembarangan dihapus di tengah jalan ketika melakukan update aplikasi? statefullset jawabannya.

cara kerja statefullset adalah setiap pod mengklaim pvcnya masing masing (tidak boleh shared pvc).
statefull akan memastikan bahwa nama pod yang konsisten (tidak random seperti rs/rc), identitas network yang stabil dan persistent volume yang stabil untik tiap pod, jika ada ada pod yang mati maka akan membuat nama dan informasi yang sama dengan pod mati tersebut.


Kubernetes Dashboard:
untuk melihat dan mengelola object dengan GUI, aktif di namespace yang berbeda dengan default