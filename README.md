### Apache Flink

Bir süredir google cloud üzerindeki cluster'ımıza kullanımımıza uygun apache flink kurulumu yapmak için uğraşıyorduk. 
Flink kurulumunda bizim için uygun kurulumu bulana kadar çok uğraştığımız için bir yerlerde türkçe bir kaynak olması açısından bu repo’yu hazırlamak istedim. Flink bilgim sınırlı olduğu için elimden geldiğince size, google cloud üzerinde var olan cluster'ımıza flink kurulumunda yaptığımız adımlardan bahsetmeye çalışacağım.


Apache Flink, ölçeklenebilir(scalable), dağıtılmış(distributed), hataya dayanıklı (fault-tolerant) ve durum bilgili akış işleme (stateful stream) yetenekleri sağlayan açık kaynaklı bir platformdur. Flink, en yeni ve öncü Büyük Veri işleme framework’lerinden biridir. Tüm cluster ortamlarında, in-memory hızda ve her ölçekte hesaplama yapacak şekilde tasarlanmıştır. 

Apache Flink, türetilmiş akışları Apache Kafka, DB'ler ve Elastic search gibi diğer hizmetlere veya uygulamalara göndermeden önce, farklı kaynaklardan büyük akış verilerini (birkaç terabayta kadar) almanıza ve bunları birden çok düğümde dağıtılmış bir şekilde işlemesine olanak tanır. Basitçe, bir Flink ardışık düzeninin temel yapı taşları: girdi, işleme ve çıktı. Çalışma zamanı, hataya dayanıklı bir şekilde son derece yüksek çıktılarda düşük gecikmeli işlemeyi destekler. Flink yetenekleri, veri akışı ve olay tabanlı yeteneklerden gerçek zamanlı içgörüler sağlar. Flink, streaming data üzerinde real-time data analytics  sağlar. Extract-transform-load (ETL) işlem hatlarına ve event-driven uygulamalara iyi uyum sağlar.

Flink, Job Manager ve Task Manager olmak üzere iki bileşenden oluşur:

* Job Manager, akış işleme işini koordine eder. Job Dataflow graph’ını aldıktan sonra, yürütme(execution) graph’ını oluşturmaktan sorumludur. Daha sonra işi Task manager'a atar ve işin yürütülmesini denetler.
* Task manager, gerçek akış işleme mantığını yürütür. Job Manager tarafından atanan işleri yürütüp belirlenen paralellikte çalışarak görevlerin durumunu job manager’a göndermekle sorumludur. 
Her zaman en azından bir aktif job manager olmalıdır. n tane Task manager olabilir. Flink, çeşitli akış işleme operatörlerinin durumunu depolamada periyodik olarak Checkpointing'i kullanır. Bir sorundan kurtarılırken, akış işleme işi en son kontrol noktasından devam edebilir. Kontrol noktası belirleme, Job Manager tarafından koordine edilir - özellikle, Job Manager, daha sonra önem kazanacak olan en son tamamlanan kontrol noktasının konumunu bilir.

## Apache Flink Kubernetes Deployment

 Kubernetes kurulumuda iki ana yaklaşım var.
1)	Standalone Mode
2)	Kubernetes Native

# Standalone Mode

Standalone Mod’da kurulumda, bir job manager, en az bir task manager, job manager servis exposing ve UI service portu üzerinden ingress tanımları yaptık. Bu kurulum da task manager için kaynakların tanımlarını kendimiz yapmalıyız. 
İlgili yaml dosyalarını [Standalone](./Standalone) altında bulabilirsiniz.
 
# Kubernetes Native
Bu kurulumda  Task manager deploy yapılmaz onun yerine sadece job Manager deploy edilir ve task manager job manager üzerinde yapılan slot yapılandırması ile job manager tarafından yönetilir.
Cluster'ın config dosyası configmap'de tanımlı flink-conf.yaml dosyasından okunuyor. configmap altında ayrıca log4j-console.properties ve logback-console.xml dosyalarıda bulunmakta.
Deployment yaml'ında configmap'i volume olarak pod'a bağladığımızı görebilirsiniz. Configmap içindeki dosyaları ayrıca repoya ekledim böylece daha biçimli şekilde yapılandırmaları görebilirsiniz.
Bu kurulumda savepoint'i ve checkpoint'i config dosyasında belirtmeniz gerekiyor.

İlgili yaml dosyalarını [Native](./Native) altında bulabilirsiniz.

Biz kubernetes native kurulumunu kullanmayı tercih ettik bunun sebebi task manager'ın resource yönetimini yapılcak işe göre job manager'ın yönetiyor olması.
