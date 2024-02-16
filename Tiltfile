docker_build('coordinator',
             context='.',
             dockerfile='./go/coordinator/Dockerfile'
)

docker_build('server',
             context='.',
             dockerfile='./Dockerfile',
)

docker_build('worker',
             context='.',
             dockerfile='./rust/worker/Dockerfile'
)


k8s_yaml(['k8s/dev/setup.yaml'])
k8s_resource(
  objects=['chroma:Namespace', 'memberlist-reader:ClusterRole', 'memberlist-reader:ClusterRoleBinding', 'pod-list-role:Role', 'pod-list-role-binding:RoleBinding', 'memberlists.chroma.cluster:CustomResourceDefinition','worker-memberlist:MemberList', 'test-memberlist:MemberList'],
  new_name='k8s_setup',
  labels=["infrastructure"]
)
k8s_yaml(['k8s/dev/pulsar.yaml'])
k8s_resource('pulsar', resource_deps=['k8s_setup'], labels=["infrastructure"], port_forwards=6650)
k8s_yaml(['k8s/dev/server.yaml'])
k8s_resource('server', resource_deps=['k8s_setup'],labels=["chroma"], port_forwards=8000 )
k8s_yaml(['k8s/dev/coordinator.yaml'])
k8s_resource('coordinator', resource_deps=['pulsar', 'server'], labels=["chroma"], port_forwards=50051)
k8s_yaml(['k8s/dev/worker.yaml'])
k8s_resource('worker', resource_deps=['coordinator'],labels=["chroma"])
