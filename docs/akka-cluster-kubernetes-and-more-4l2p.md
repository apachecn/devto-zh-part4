# Akka Cluster、Kubernetes 等

> 原文：<https://dev.to/irajbalakrish/akka-cluster-kubernetes-and-more-4l2p>

[![Alt Text](img/ed8fd52518726226b777db1bbc92d592.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EDgpeqGv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3uxcuwnuwm6s6qdo1mi8.jpg)

[![Alt Text](img/2ffddd15efd344e89729ed58dde09e50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D07GCNdG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/036qq87n5vtlblmr1a7j.jpg)

[![Alt Text](img/67cfea5b5f230da86069f1359f50fccf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tPwXwPN7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aaaqoihwmgfurz9fay3l.jpg)

[![Alt Text](img/07b6bcf708c298fd494cd0acceff9478.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3r0yv_Mk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rqau9s6w6ki6dl57pv88.jpg)

[![Alt Text](img/748db427f975258b934463b9b6620e75.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tzCOiK7J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1of35naido0pvofvkr17.jpg)

[![Alt Text](img/7c2b3654d983137c01193775ef73e8d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---d1Asa6d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z7vxt6s77upa0y1qw3nm.jpg)

[![Alt Text](img/4465617c398aa04be91bb90d7cec6bd7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h_zQxIvU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o3t5zalhiup9jcimuh88.jpg)

[![Alt Text](img/36789e51567f792923777d10fd51d6b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5ONAWCN1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3zi68fnc371rih4fp4sz.jpg)

[![Alt Text](img/98ff1eb715fa8ce92abe269df22f72d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KfDv4_Lx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cx44jxa94zvgdf3aovtz.jpg)

[![Alt Text](img/c329314da7ccaf8ba7277e5edc4f2dc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7Zo4H13L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vnye0sy4m8hvnqlfmx9g.jpg)

[![Alt Text](img/d378a20d5865acc2ba68f9e9babe57dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I97Faj4c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ez75usgvcvbdoy3crur2.jpg)

[![Alt Text](img/f8ed3c2fb49cc836740ef65bc828fca5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VEJPZPTm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t5km74owj0tnt4dkxoad.jpg)

[![Alt Text](img/3d81e8144aed77c7d548515316477d41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3r3_5eTW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l7zvnnred6993i12rb5n.jpg)

[![Alt Text](img/da7e799860273e05d71319c2bd069513.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JAIYeafl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xbwyuxsgqp1gmp6gajho.jpg)

[![Alt Text](img/8f47fa29b7296826a245347443c332eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nvfPCCnN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mdgup464bszyo7n1ghvt.jpg)

[![Alt Text](img/5cbb47aff6c05f13c37b1facbfd0708a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HCIM89cm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gkq3rpni0uz25amzgivt.jpg)

[![Alt Text](img/aa671ca7c86bc076ef7b1dc2dbb514c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kxNe0yPs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n661hrlqoijtasxo3ju4.jpg)

[![Alt Text](img/80388f25936ee9ef9b88a2ec57648c66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_t_cKPLG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2kj1ubc4naikzl500et1.jpg)