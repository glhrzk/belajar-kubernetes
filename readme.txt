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

Pod:
kenapa butuh pod? kenapa gak langsung container?: karena pod flexibel,  isolasi, skalabilitas, managemen resource.
Pod adalah unit terkecil yang bisa di deploy ke Kubernetes cluster atau bisa dibilang Grup dari kontainer, Pod berisi satu atau lebih dari container.
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
Readiness Probe: melakukan pengecekan, jika respon dari pengecekan tidak sehat/unhealthy maka akan menghentikan/stop rx traffic pod tersebut
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
jika kita ingin menjalankan pod di setiap node yang ada (disetiap node dipasang pod/aplikasi), kita bisa mengaturnya secara manual dengan daemon set.
 contoh kasus:
 1. aplikasi untuk monitoring node
 2. aplikasi untuk mengambil lode node

Job:
job adalah resource di kubernetes yang digunakan untuk menjalan pod hanya sekali.
berbeda dengan rc, rs, dan ds yang akan membuat secara otomatis ketika pod tersebut hilang/rusak.
job akan mati jika pekerjaannya akan selesai.
    contoh kasus:
    1. backup atau restore database
    2. import atay expore data
    3. aplikasi batch

