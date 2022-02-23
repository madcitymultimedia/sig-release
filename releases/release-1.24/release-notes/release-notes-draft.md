## Urgent Upgrade Notes 

### (No, really, you MUST read this before you upgrade)

- Docker runtime support using dockershim in the kubelet is now completely removed in 1.24. The kubelet used to have a module called dockershim, which implements CRI support for Docker, and it has seen maintenance issues in the Kubernetes community. From 1.24 onwards, please move to a container runtime that is a full-fledged implementation of CRI (v1alpha1 or v1 compliant) as they become available. ([#97252](https://github.com/kubernetes/kubernetes/pull/97252), [@dims](https://github.com/dims))
 - The calculations for Pod topology spread skew now excludes nodes that
  don't match the node affinity/selector. This may lead to unschedulable pods if you previously had pods
  matching the spreading selector on those excluded nodes (not matching the node affinity/selector),
  especially when the topologyKey is not node-level. Revisit the node affinity and/or pod selector in the
  topology spread constraints to avoid this scenario. ([#107009](https://github.com/kubernetes/kubernetes/pull/107009), [@kerthcet](https://github.com/kerthcet))
 
## Changes by Kind

### Deprecation

- "kubeadm.k8s.io/v1beta2" has been deprecated and will be removed in a future release, possibly in 3 releases (one year). You should start using "kubeadm.k8s.io/v1beta3" for new clusters. To migrate your old configuration files on disk you can use the "kubeadm config migrate" command. ([#107013](https://github.com/kubernetes/kubernetes/pull/107013), [@pacoxu](https://github.com/pacoxu))
- Deprecated `Service.Spec.LoadBalancerIP`. This field was under-specified and its meaning varies across implementations.  As of Kubernetes v1.24, users are encouraged to use implementation-specific annotations when available.  This field may be removed in a future API version. ([#107235](https://github.com/kubernetes/kubernetes/pull/107235), [@uablrek](https://github.com/uablrek))
- Kube-apiserver: the insecure address flags `--address`, `--insecure-bind-address`, `--port` and `--insecure-port` (inert since 1.20) are removed ([#106859](https://github.com/kubernetes/kubernetes/pull/106859), [@knight42](https://github.com/knight42))
- The experimental dynamic log sanitization feature has been deprecated and removed in the 1.24 release. The feature is no longer available for use. ([#107207](https://github.com/kubernetes/kubernetes/pull/107207), [@ehashman](https://github.com/ehashman))
- The insecure address flags `--address` and `--port` in kube-controller-manager have been no effect since v1.20 and is removed in v1.24. ([#106860](https://github.com/kubernetes/kubernetes/pull/106860), [@knight42](https://github.com/knight42))

### API Change

- Added a new metric `webhook_fail_open_count` to monitor webhooks that fail to open. ([#107171](https://github.com/kubernetes/kubernetes/pull/107171), [@ltagliamonte-dd](https://github.com/ltagliamonte-dd))
- Fixed failed flushing logs in defer function when kubelet cmd exit 1. ([#104774](https://github.com/kubernetes/kubernetes/pull/104774), [@kerthcet](https://github.com/kerthcet))
- Renamed metrics `evictions_number` to `evictions_total` and mark it as stable. The original `evictions_number` metrics name is marked as "Deprecated" and has been removed in kubernetes 1.23 . ([#106366](https://github.com/kubernetes/kubernetes/pull/106366), [@cyclinder](https://github.com/cyclinder))
- The `ServiceLBNodePortControl` feature graduates to GA. The feature gate will be removed in 1.26. ([#107027](https://github.com/kubernetes/kubernetes/pull/107027), [@uablrek](https://github.com/uablrek))
- The feature `DynamicKubeletConfig` has been removed from the kubelet. ([#106932](https://github.com/kubernetes/kubernetes/pull/106932), [@SergeyKanzhelev](https://github.com/SergeyKanzhelev))
- Updated the default API priority-and-fairness config to avoid endpoint/configmaps operations from controller-manager to all match leader-election priority level. ([#106725](https://github.com/kubernetes/kubernetes/pull/106725), [@wojtek-t](https://github.com/wojtek-t))

### Feature

- A new Priority and Fairness metric 'apiserver_flowcontrol_work_estimate_seats_samples' has been added that tracks the estimated seats associated with a request. ([#106628](https://github.com/kubernetes/kubernetes/pull/106628), [@tkashem](https://github.com/tkashem))
- Added `SetTransform` to `SharedInformer` to allow users to transform objects before they are stored. ([#107507](https://github.com/kubernetes/kubernetes/pull/107507), [@alexzielenski](https://github.com/alexzielenski))
- Added a metric for measuring end-to-end volume mount timing. ([#107006](https://github.com/kubernetes/kubernetes/pull/107006), [@gnufied](https://github.com/gnufied))
- Added a new Priority and Fairness metric apiserver_flowcontrol_request_dispatch_no_accommodation_total' to track the number of times a request dispatch attempt results in a no-accommodation status due to lack of available seats. ([#106629](https://github.com/kubernetes/kubernetes/pull/106629), [@tkashem](https://github.com/tkashem))
- Added a path `/header?key=` to `agnhost netexec` allowing one to view what the header value is of the incoming request.
  
  Ex:
  
  $ curl -H "X-Forwarded-For: something" 172.17.0.2:8080/header?key=X-Forwarded-For
  something ([#107796](https://github.com/kubernetes/kubernetes/pull/107796), [@alexanderConstantinescu](https://github.com/alexanderConstantinescu))
- Added a proxy-url flag into kubectl config set-cluster. ([#105566](https://github.com/kubernetes/kubernetes/pull/105566), [@ardaguclu](https://github.com/ardaguclu))
- Added completion for `kubectl config set-context`. ([#106739](https://github.com/kubernetes/kubernetes/pull/106739), [@kebe7jun](https://github.com/kebe7jun))
- Added field add_ambient_capabilities to the Capabilities message in the CRI-API. ([#104620](https://github.com/kubernetes/kubernetes/pull/104620), [@vinayakankugoyal](https://github.com/vinayakankugoyal))
- Added label selector flag to all "kubectl rollout" commands. ([#99758](https://github.com/kubernetes/kubernetes/pull/99758), [@aramperes](https://github.com/aramperes))
- Added more message for no PodSandbox container. ([#107116](https://github.com/kubernetes/kubernetes/pull/107116), [@yxxhero](https://github.com/yxxhero))
- Added prune flag into diff command to simulate `apply --prune`. ([#105164](https://github.com/kubernetes/kubernetes/pull/105164), [@ardaguclu](https://github.com/ardaguclu))
- Added support for kubectl commands (`kubectl exec` and `kubectl port-forward`) via a SOCKS5 proxy. ([#105632](https://github.com/kubernetes/kubernetes/pull/105632), [@xens](https://github.com/xens))
- Feature of `PreferNominatedNode` is graduated  to GA. ([#106619](https://github.com/kubernetes/kubernetes/pull/106619), [@chendave](https://github.com/chendave))
- In text format, log messages that previously used quoting to prevent multi-line output (for example, text="some \"quotation\", a\nline break") will now be printed with more readable multi-line output without the escape sequences. ([#107103](https://github.com/kubernetes/kubernetes/pull/107103), [@pohly](https://github.com/pohly))
- Kube-apiserver: when merging lists, Server Side Apply now prefers the order of the submitted request instead of the existing persisted object. ([#107565](https://github.com/kubernetes/kubernetes/pull/107565), [@jiahuif](https://github.com/jiahuif))
- Kubeadm: added support for dry running "kubeadm reset". The new flag "kubeadm reset --dry-run" is similar to the existing flag for "kubeadm init/join/upgrade" and allows you to see what changes would be applied. ([#107512](https://github.com/kubernetes/kubernetes/pull/107512), [@SataQiu](https://github.com/SataQiu))
- Kubeadm: default the kubeadm configuration to the containerd socket (Unix: unix:///var/run/containerd/containerd.sock, Windows: "npipe:////./pipe/containerd-containerd") instead of the one for Docker. If the "Init|JoinConfiguration.nodeRegistration.criSocket" field is empty during cluster creation and multiple sockets are found on the host always throw an error and ask the user to specify which one to use by setting the value in the field. Make sure you update any kubeadm configuration files on disk, to not include the dockershim socket unless you are still using kubelet version < 1.24 with kubeadm >= 1.24.
  
  Remove the DockerValidor and ServiceCheck for the "docker" service from kubeadm preflight. Docker is no longer special cased during host validation and ideally this task should be done in the now external cri-dockerd project where the importance of the compatibility matters.
  
  Use crictl for all communication with CRI sockets for actions like pulling images and obtaining a list of running containers instead of using the docker CLI in the case of Docker. ([#107317](https://github.com/kubernetes/kubernetes/pull/107317), [@neolit123](https://github.com/neolit123))
- Kubectl logs will now warn and default to the first container in a pod. This new behavior brings it in line with kubectl exec. ([#105964](https://github.com/kubernetes/kubernetes/pull/105964), [@kidlj](https://github.com/kidlj))
- Kubelet: following dockershim related flags are also removed along with dockershim `--experimental-dockershim-root-directory`, `--docker-endpoint`, `--image-pull-progress-deadline`, `--network-plugin`, `--cni-conf-dir`, `--cni-bin-dir`, `--cni-cache-dir`, `--network-plugin-mtu`. ([#106907](https://github.com/kubernetes/kubernetes/pull/106907), [@cyclinder](https://github.com/cyclinder))
- Kubernetes is now built with Golang 1.17.4 ([#106833](https://github.com/kubernetes/kubernetes/pull/106833), [@cpanato](https://github.com/cpanato))
- Kubernetes is now built with Golang 1.17.5. ([#106956](https://github.com/kubernetes/kubernetes/pull/106956), [@cpanato](https://github.com/cpanato))
- Kubernetes is now built with Golang 1.17.6. ([#107612](https://github.com/kubernetes/kubernetes/pull/107612), [@palnabarun](https://github.com/palnabarun))
- OpenStack Cinder CSI migration is now GA and switched on by default, Cinder CSI driver must be installed on clusters on OpenStack for Cinder volumes to work (has been since v1.21). ([#107462](https://github.com/kubernetes/kubernetes/pull/107462), [@dims](https://github.com/dims))
- Removed kube-scheduler insecure flags. You can use `--bind-address` and `--secure-port` instead. ([#106865](https://github.com/kubernetes/kubernetes/pull/106865), [@jonyhy96](https://github.com/jonyhy96))
- Removed the `ImmutableEphemeralVolumes` feature gate. ([#107152](https://github.com/kubernetes/kubernetes/pull/107152), [@mengjiao-liu](https://github.com/mengjiao-liu))
- The `NamespaceDefaultLabelName` feature gate, GA since v1.22, is now removed. ([#106838](https://github.com/kubernetes/kubernetes/pull/106838), [@mengjiao-liu](https://github.com/mengjiao-liu))
- Updated golang.org/x/net to v0.0.0-20211209124913-491a49abca63. ([#106949](https://github.com/kubernetes/kubernetes/pull/106949), [@cpanato](https://github.com/cpanato))

### Bug or Regression

- Added a new label `type` to `apiserver_flowcontrol_request_execution_seconds` metric - it has the following values: - 'regular': indicates that it is a non long running request - 'watch': indicates that it is a watch request. ([#105517](https://github.com/kubernetes/kubernetes/pull/105517), [@tkashem](https://github.com/tkashem))
- Added a test to guarantee that conformance clusters require at least 2 untainted nodes. ([#106313](https://github.com/kubernetes/kubernetes/pull/106313), [@aojea](https://github.com/aojea))
- Allowed attached volumes to be mounted quicker by skipping exp. backoff when checking for reported-in-use volumes. ([#106853](https://github.com/kubernetes/kubernetes/pull/106853), [@gnufied](https://github.com/gnufied))
- Alowed useful inclusion of `-args $prog_args` in KUBE_TEST_ARGS, when doing `make test-integration`. ([#107516](https://github.com/kubernetes/kubernetes/pull/107516), [@MikeSpreitzer](https://github.com/MikeSpreitzer))
- An inefficient lock in EndpointSlice controller metrics cache has been reworked. Network programming latency may be significantly reduced in certain scenarios, especially in clusters with a large number of Services. ([#107091](https://github.com/kubernetes/kubernetes/pull/107091), [@robscott](https://github.com/robscott))
- Apiserver will now reject connection attempts to 0.0.0.0/:: when handling a proxy subresource request. ([#107402](https://github.com/kubernetes/kubernetes/pull/107402), [@anguslees](https://github.com/anguslees))
- Apiserver, if configured to reconcile the kubernetes.default service endpoints, checks if the configured Service IP range matches the apiserver public address IP family, and fails to start if not. ([#106721](https://github.com/kubernetes/kubernetes/pull/106721), [@aojea](https://github.com/aojea))
- Changed node staging path for csi driver to use a PV agnostic path. Nodes must be drained before updating the kubelet with this change. ([#107065](https://github.com/kubernetes/kubernetes/pull/107065), [@saikat-royc](https://github.com/saikat-royc))
- Client-go: fixed the paged list calls with ResourceVersionMatch set would fail once paging is kicked in. ([#107311](https://github.com/kubernetes/kubernetes/pull/107311), [@fasaxc](https://github.com/fasaxc))
- Fix libct/cg/fs2: fixed GetStats for unsupported hugetlb error on Raspbian Bullseye ([#106912](https://github.com/kubernetes/kubernetes/pull/106912), [@Letme](https://github.com/Letme))
- Fix: deleted a non existing Azure disk issue. ([#107406](https://github.com/kubernetes/kubernetes/pull/107406), [@andyzhangx](https://github.com/andyzhangx))
- Fix: removed outdated ipv4 route when the corresponding node is deleted. ([#106164](https://github.com/kubernetes/kubernetes/pull/106164), [@nilo19](https://github.com/nilo19))
- Fixed Azurefile volumeid collision issue in csi migration. ([#107575](https://github.com/kubernetes/kubernetes/pull/107575), [@andyzhangx](https://github.com/andyzhangx))
- Fixed a bug that a pod's `.status.nominatedNodeName` is not cleared properly, and thus over-occupied system resources. ([#106816](https://github.com/kubernetes/kubernetes/pull/106816), [@Huang-Wei](https://github.com/Huang-Wei))
- Fixed a bug that could cause a panic when a /healthz request times out. ([#107034](https://github.com/kubernetes/kubernetes/pull/107034), [@benluddy](https://github.com/benluddy))
- Fixed a bug where unwanted fields were being returned from a create dry-run: uid and, if generateName was used, name. ([#107088](https://github.com/kubernetes/kubernetes/pull/107088), [@joejulian](https://github.com/joejulian))
- Fixed a bug where vSphere client connections where not being closed during testing. Leaked vSphere client sessions were causing resource exhaustion during automated testing. ([#107337](https://github.com/kubernetes/kubernetes/pull/107337), [@derek-pryor](https://github.com/derek-pryor))
- Fixed a panic when using invalid output format in kubectl create secret command. ([#107221](https://github.com/kubernetes/kubernetes/pull/107221), [@rikatz](https://github.com/rikatz))
- Fixed a rare race condition handling requests that timeout. ([#107452](https://github.com/kubernetes/kubernetes/pull/107452), [@liggitt](https://github.com/liggitt))
- Fixed a regression in 1.23 that incorrectly pruned data from array items of a custom resource that set `x-kubernetes-preserve-unknown-fields: true`. ([#107688](https://github.com/kubernetes/kubernetes/pull/107688), [@liggitt](https://github.com/liggitt))
- Fixed a regression in 1.23 where update requests to previously persisted `Service` objects that have not been modified since 1.19 can be rejected with an incorrect `spec.clusterIPs: Required value` error. ([#107847](https://github.com/kubernetes/kubernetes/pull/107847), [@thockin](https://github.com/thockin))
- Fixed azuredisk parameter lowercase translation issue. ([#107429](https://github.com/kubernetes/kubernetes/pull/107429), [@andyzhangx](https://github.com/andyzhangx))
- Fixed bug in error messaging for basic-auth and ssh secret validations. ([#106179](https://github.com/kubernetes/kubernetes/pull/106179), [@vivek-koppuru](https://github.com/vivek-koppuru))
- Fixed detaching CSI volumes from nodes when a CSI driver name has prefix "csi-". ([#107025](https://github.com/kubernetes/kubernetes/pull/107025), [@jsafrane](https://github.com/jsafrane))
- Fixed duplicate port opening in kube-proxy when `--nodeport-addresses` is empty. ([#107413](https://github.com/kubernetes/kubernetes/pull/107413), [@tnqn](https://github.com/tnqn))
- Fixed handling of objects with invalid selectors. ([#107559](https://github.com/kubernetes/kubernetes/pull/107559), [@liggitt](https://github.com/liggitt))
- Fixed kubectl bug where bash completions don't work if --context flag is specified with a value that contains a colon. ([#107439](https://github.com/kubernetes/kubernetes/pull/107439), [@brianpursley](https://github.com/brianpursley))
- Fixed performance regression in JSON logging caused by syncing stdout every time error was logged. ([#107035](https://github.com/kubernetes/kubernetes/pull/107035), [@serathius](https://github.com/serathius))
- Fixed regression in CPUManager that it will release exclusive CPUs in app containers inherited from init containers when the init containers were removed. ([#104837](https://github.com/kubernetes/kubernetes/pull/104837), [@eggiter](https://github.com/eggiter))
- Fixed static pod add and removes restarts in certain cases. ([#107695](https://github.com/kubernetes/kubernetes/pull/107695), [@rphillips](https://github.com/rphillips))
- Improved handling of unmount failures when device may be in-use by another container/process. ([#107789](https://github.com/kubernetes/kubernetes/pull/107789), [@gnufied](https://github.com/gnufied))
- Improved the rounding of PodTopologySpread scores to offer better scoring when spreading a low number of pods. ([#107384](https://github.com/kubernetes/kubernetes/pull/107384), [@sanposhiho](https://github.com/sanposhiho))
- Kube-apiserver: Server Side Apply merge order is reverted to match v1.22 behavior until http://issue.k8s.io/104641 is resolved. ([#106660](https://github.com/kubernetes/kubernetes/pull/106660), [@liggitt](https://github.com/liggitt))
- Kubeadm: allow the `certs check-expiration` command to not require the existence of the cluster CA key (ca.key file) when checking the expiration of managed certificates in kubeconfig files. ([#106854](https://github.com/kubernetes/kubernetes/pull/106854), [@neolit123](https://github.com/neolit123))
- Kubeadm: during execution of the "check expiration" command, treat the etcd CA as external if there is a missing etcd CA key file (etcd/ca.key) and perform the proper validation on certificates signed by the etcd CA. Additionally, make sure that the CA for all entries in the output table is included - for both certificates on disk and in kubeconfig files. ([#106891](https://github.com/kubernetes/kubernetes/pull/106891), [@neolit123](https://github.com/neolit123))
- Metrics Server image bumped to v0.5.2 ([#106492](https://github.com/kubernetes/kubernetes/pull/106492), [@serathius](https://github.com/serathius))
- Modified command line errors (for example, "kubectl list" -> "unknown command") that were printed as log message with escaped line breaks instead of a multi-line plain text, making the error hard to read. ([#107044](https://github.com/kubernetes/kubernetes/pull/107044), [@pohly](https://github.com/pohly))
- Modified log messages that were logged with `"v":0` in JSON output although they were debug messages with a higher verbosity. ([#106978](https://github.com/kubernetes/kubernetes/pull/106978), [@pohly](https://github.com/pohly))
- No ([#107769](https://github.com/kubernetes/kubernetes/pull/107769), [@liurupeng](https://github.com/liurupeng)) [SIG Cloud Provider and Windows]
- NodeRestriction admission: nodes are now allowed to update PersistentVolumeClaim status fields `resizeStatus` and `allocatedResources` when the `RecoverVolumeExpansionFailure` feature is enabled. ([#107686](https://github.com/kubernetes/kubernetes/pull/107686), [@gnufied](https://github.com/gnufied))
- Only extend token lifetimes when `--service-account-extend-token-expiration` is true and the requested token audiences are empty or exactly match all values for `--api-audiences`. ([#105954](https://github.com/kubernetes/kubernetes/pull/105954), [@jyotimahapatra](https://github.com/jyotimahapatra))
- Publishing kube-proxy metrics for Windows kernel-mode ([#106581](https://github.com/kubernetes/kubernetes/pull/106581), [@knabben](https://github.com/knabben))
- Removed validation if AppArmor profiles are loaded on the local node. This should be handled by the
  container runtime. ([#97966](https://github.com/kubernetes/kubernetes/pull/97966), [@saschagrunert](https://github.com/saschagrunert))
- Restored `NumPDBViolations` info of nodes, when `HTTPExtender ProcessPreemption`. This info will be used in subsequent filtering steps - `pickOneNodeForPreemption` ([#105853](https://github.com/kubernetes/kubernetes/pull/105853), [@caden2016](https://github.com/caden2016))
- Reverts graceful node shutdown to match 1.21 behavior of setting pods that have not yet successfully completed to "Failed" phase if the GracefulNodeShutdown feature is enabled in kubelet. The GracefulNodeShutdown feature is beta and must be explicitly configured via kubelet config to be enabled in 1.21+. This changes 1.22 and 1.23 behavior on node shutdown to match 1.21. If you do not want pods to be marked terminated on node shutdown in 1.22 and 1.23, disable the GracefulNodeShutdown feature. ([#106901](https://github.com/kubernetes/kubernetes/pull/106901), [@bobbypage](https://github.com/bobbypage))
- Reverts the CRI API version surfaced by dockershim to v1alpha2 ([#106803](https://github.com/kubernetes/kubernetes/pull/106803), [@saschagrunert](https://github.com/saschagrunert))
- The Service field `spec.internalTrafficPolicy` is no longer defaulted for Services when the type is ExternalName. The field is also dropped on read when the Service type is ExternalName. ([#104846](https://github.com/kubernetes/kubernetes/pull/104846), [@andrewsykim](https://github.com/andrewsykim))
- The deprecated flag `--really-crash-for-testing` was removed. ([#101719](https://github.com/kubernetes/kubernetes/pull/101719), [@SergeyKanzhelev](https://github.com/SergeyKanzhelev))
- The feature gate was mentioned as `csiMigrationRBD` where it should have been `CSIMigrationRBD` to be in parity with other migration plugins. This release correct the same and keep it as `CSIMigrationRBD`.
  
  users who have configured this feature gate as `csiMigrationRBD` has to reconfigure the same to `CSIMigrationRBD` from this release. ([#107554](https://github.com/kubernetes/kubernetes/pull/107554), [@humblec](https://github.com/humblec))

### Other (Cleanup or Flake)

- Added an example for the `kubectl plugin list` command. ([#106600](https://github.com/kubernetes/kubernetes/pull/106600), [@bergerhoffer](https://github.com/bergerhoffer))
- Added details about preemption in the event for scheduling failed. ([#107775](https://github.com/kubernetes/kubernetes/pull/107775), [@denkensk](https://github.com/denkensk))
- Build/dependencies.yaml: remove the dependency on Docker. With the dockershim removal, core Kubernetes no longer
  has to track the latest validated version of Docker. ([#107607](https://github.com/kubernetes/kubernetes/pull/107607), [@neolit123](https://github.com/neolit123))
- Deprecates types in `k8s.io/apimachinery/util/clock`. Please use `k8s.io/utils/clock` instead. ([#106850](https://github.com/kubernetes/kubernetes/pull/106850), [@MadhavJivrajani](https://github.com/MadhavJivrajani))
- E2e tests wait for kube-root-ca.crt to be populated in namespaces for use with projected service account tokens, reducing delays starting those test pods and errors in the logs. ([#107763](https://github.com/kubernetes/kubernetes/pull/107763), [@smarterclayton](https://github.com/smarterclayton))
- Fixed documentation typo in cloud-provider. ([#106445](https://github.com/kubernetes/kubernetes/pull/106445), [@majst01](https://github.com/majst01))
- Fixed spelling of implemented in pkg/proxy/apis/config/types.go line 206 ([#106453](https://github.com/kubernetes/kubernetes/pull/106453), [@davidleitw](https://github.com/davidleitw))
- Kubeadm: all warning messages are printed to stderr instead of stdout. ([#107467](https://github.com/kubernetes/kubernetes/pull/107467), [@SataQiu](https://github.com/SataQiu))
- Kubeadm: handled the removal of dockershim related flags for new kubeadm clusters. If kubelet <1.24 is on the host, kubeadm >=1.24 can continue using the built-in dockershim in the kubelet if the user passes the "{Init|Join}Configuration.nodeRegistration.criSocket" value in the kubeadm configuration to be equal to "unix:///var/run/dockershim.sock" on Unix or "npipe:////./pipe/dockershim" on Windows. If kubelet version >=1.24 is on the host, kubeadm >=1.24 will treat all container runtimes as "remote" using the kubelet flags "--container-runtime=remote --container-runtime-endpoint=scheme://some/path". The special management for kubelet <1.24 will be removed in kubeadm 1.25. ([#106973](https://github.com/kubernetes/kubernetes/pull/106973), [@neolit123](https://github.com/neolit123))
- Kubeadm: make sure that "kubeadm init/join" always use a URL scheme (unix:// on Linux and npipe:// on Windows) when passing a value to the "--container-runtime-endpoint" kubelet flag. This flag's value is taken from the kubeadm configuration "criSocket" field or the "--cri-socket" CLI flag. Automatically add a missing URL scheme to the user configuration in memory, but warn them that they should also update their configuration on disk manually. During "kubeadm upgrade apply/node" mutate the "/var/lib/kubelet/kubeadm-flags.env" file on disk and the "kubeadm.alpha.kubernetes.io/cri-socket" annotation Node object if needed. These automatic actions are temporary and will be removed in a future release. In the future the kubelet may not support CRI endpoints without an URL scheme. ([#107295](https://github.com/kubernetes/kubernetes/pull/107295), [@neolit123](https://github.com/neolit123))
- Kubeadm: remove the `IPv6DualStack` feature gate. The feature has been GA and locked to enabled since 1.23. ([#106648](https://github.com/kubernetes/kubernetes/pull/106648), [@calvin0327](https://github.com/calvin0327))
- Kubeadm: removed the deprecated output/v1alpha1 API used for machine readable output by some kubeadm commands. In 1.23 kubeadm started using the newer version output/v1alpha2 for the same purpose. ([#107468](https://github.com/kubernetes/kubernetes/pull/107468), [@neolit123](https://github.com/neolit123))
- Kubeadm: removed the restriction that the ca.crt can only contain one certificate. If there is more than one certificate in the ca.crt file, kubeadm will pick the first one by default. ([#107327](https://github.com/kubernetes/kubernetes/pull/107327), [@SataQiu](https://github.com/SataQiu))
- Kubectl: restored `--dry-run`, `--dry-run=true`, and `--dry-run=false` for compatibility with pre-1.23 invocations. ([#107003](https://github.com/kubernetes/kubernetes/pull/107003), [@julianvmodesto](https://github.com/julianvmodesto))
- Kubelet config validation error messages are updated ([#105360](https://github.com/kubernetes/kubernetes/pull/105360), [@shuheiktgw](https://github.com/shuheiktgw))
- Kubernetes e2e framework will use the url "invalid.registry.k8s.io/invalid" instead "invalid.com/invalid" for test that use an invalid registry. ([#107455](https://github.com/kubernetes/kubernetes/pull/107455), [@aojea](https://github.com/aojea))
- Marked kubelet `--container-runtime-endpoint` and `--image-service-endpoint` CLI flags as stable. ([#106954](https://github.com/kubernetes/kubernetes/pull/106954), [@saschagrunert](https://github.com/saschagrunert))
- Migrate volume/csi/csi-client.go logs to structured logging. ([#99441](https://github.com/kubernetes/kubernetes/pull/99441), [@CKchen0726](https://github.com/CKchen0726))
- Refactor kubelet command line for enabling features and drop "RuntimeClass" if present. Note that this feature has been on by default since 1.14 and was GA'ed in 1.20. ([#106882](https://github.com/kubernetes/kubernetes/pull/106882), [@cyclinder](https://github.com/cyclinder))
- The `--container-runtime kubelet flag is deprecated and will be removed in future releases. ([#107094](https://github.com/kubernetes/kubernetes/pull/107094), [@adisky](https://github.com/adisky))
- The fluentd-elasticsearch addon is no longer included in the cluster directory. It is available from https://github.com/kubernetes-sigs/instrumentation-addons/tree/master/fluentd-elasticsearch. ([#107553](https://github.com/kubernetes/kubernetes/pull/107553), [@liggitt](https://github.com/liggitt))
- Updated cri-tools to [v1.23.0](https://github.com/kubernetes-sigs/cri-tools/releases/tag/v1.23.0). ([#107604](https://github.com/kubernetes/kubernetes/pull/107604), [@saschagrunert](https://github.com/saschagrunert))
- Updated the error message to not use the "--max-resource-write-bytes" & "--json-patch-max-copy-bytes" string. ([#106875](https://github.com/kubernetes/kubernetes/pull/106875), [@warmchang](https://github.com/warmchang))