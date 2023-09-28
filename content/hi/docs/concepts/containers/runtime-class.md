---
reviewers:
  - Babapool
  - divya-mohan0209
title: Runtime Class रनटाइम क्लास
content_type: concept
weight: 30
hide_summary: true # Listed separately in section index
---

<!-- overview -->

{{< feature-state for_k8s_version="v1.20" state="stable" >}}

This page describes the RuntimeClass resource and runtime selection mechanism.

RuntimeClass is a feature for selecting the container runtime configuration. The container runtime
configuration is used to run a Pod's containers.
यह पृष्ठ रनटाइमक्लास संसाधन और रनटाइम चयन तंत्र का वर्णन करता है।

रनटाइमक्लास कंटेनर रनटाइम कॉन्फ़िगरेशन का चयन करने के लिए एक सुविधा है। कंटेनर रनटाइम
कॉन्फ़िगरेशन का उपयोग पॉड के कंटेनर ों को चलाने के लिए किया जाता है।

<!-- body -->

## Motivation

You can set a different RuntimeClass between different Pods to provide a balance of
performance versus security. For example, if part of your workload deserves a high
level of information security assurance, you might choose to schedule those Pods so
that they run in a container runtime that uses hardware virtualization. You'd then
benefit from the extra isolation of the alternative runtime, at the expense of some
additional overhead.

You can also use RuntimeClass to run different Pods with the same container runtime
but with different settings.
## प्रेरणा

आप संतुलन प्रदान करने के लिए विभिन्न पॉड्स के बीच एक अलग रनटाइमक्लास सेट कर सकते हैं
प्रदर्शन बनाम सुरक्षा। उदाहरण के लिए, यदि आपके कार्यभार का हिस्सा उच्च के योग्य है।
सूचना सुरक्षा आश्वासन का स्तर, आप उन पॉड्स को शेड्यूल करना चुन सकते हैं
कि वे एक कंटेनर रनटाइम में चलते हैं जो हार्डवेयर वर्चुअलाइजेशन का उपयोग करता है। तब आप करेंगे
कुछ की कीमत पर, वैकल्पिक रनटाइम के अतिरिक्त अलगाव से लाभ
अतिरिक्त ओवरहेड।

आप एक ही कंटेनर रनटाइम के साथ विभिन्न पॉड्स चलाने के लिए रनटाइमक्लास का भी उपयोग कर सकते हैं।
लेकिन अलग-अलग सेटिंग्स के साथ।


## Setup

1. Configure the CRI implementation on nodes (runtime dependent)
2. Create the corresponding RuntimeClass resources
## सेटअप

1. नोड्स पर सीआरआई कार्यान्वयन कॉन्फ़िगर करें (रनटाइम निर्भर)
2. संबंधित रनटाइमक्लास संसाधन बनाएँ

### 1. Configure the CRI implementation on nodes

The configurations available through RuntimeClass are Container Runtime Interface (CRI)
implementation dependent. See the corresponding documentation ([below](#cri-configuration)) for your
CRI implementation for how to configure.

{{< note >}}
RuntimeClass assumes a homogeneous node configuration across the cluster by default (which means
that all nodes are configured the same way with respect to container runtimes). To support
heterogeneous node configurations, see [Scheduling](#scheduling) below.
{{< /note >}}

The configurations have a corresponding `handler` name, referenced by the RuntimeClass. The
handler must be a valid [DNS label name](/docs/concepts/overview/working-with-objects/names/#dns-label-names).

### 1. नोड्स पर CRI कार्यान्वयन कॉन्फ़िगर करें

रनटाइमक्लास के माध्यम से उपलब्ध कॉन्फ़िगरेशन कंटेनर रनटाइम इंटरफ़ेस (सीआरआई) हैं।
कार्यान्वयन निर्भर है। अपने लिए संबंधित दस्तावेज ([नीचे](#cri-कॉन्फ़िगरेशन)) देखें
कॉन्फ़िगर करने के तरीके के लिए CRI कार्यान्वयन।

{{< नोट >}}
रनटाइमक्लास डिफ़ॉल्ट रूप से क्लस्टर में एक सजातीय नोड कॉन्फ़िगरेशन मानता है (जिसका अर्थ है
कि सभी नोड्स कंटेनर रनटाइम के संबंध में एक ही तरह से कॉन्फ़िगर किए गए हैं)। समर्थन करने के लिए
विषम नोड कॉन्फ़िगरेशन, नीचे [शेड्यूलिंग](#scheduling) देखें।
{{</नोट >}}

कॉन्फ़िगरेशन में एक संबंधित 'हैंडलर' नाम है, जिसे रनटाइमक्लास द्वारा संदर्भित किया गया है। वही
हैंडलर एक मान्य [डीएनएस लेबल नाम] (/डॉक्स/अवधारणाएं/अवलोकन/वर्किंग-विद-ऑब्जेक्ट्स/नाम/#dns-लेबल-नाम) होना चाहिए।

### 2. Create the corresponding RuntimeClass resources

The configurations setup in step 1 should each have an associated `handler` name, which identifies
the configuration. For each handler, create a corresponding RuntimeClass object.

The RuntimeClass resource currently only has 2 significant fields: the RuntimeClass name
(`metadata.name`) and the handler (`handler`). The object definition looks like this:

### 2. संबंधित रनटाइमक्लास संसाधन बनाएँ

चरण 1 में सेटअप किए गए कॉन्फ़िगरेशन में प्रत्येक का एक संबद्ध 'हैंडलर' नाम होना चाहिए, जो
कॉन्फ़िगरेशन. प्रत्येक हैंडलर के लिए, एक संगत रनटाइमक्लास ऑब्जेक्ट बनाएँ।

रनटाइमक्लास संसाधन में वर्तमान में केवल 2 महत्वपूर्ण फ़ील्ड हैं: रनटाइमक्लास नाम।
('metadata.name') और हैंडलर ('हैंडलर')। ऑब्जेक्ट परिभाषा इस तरह दिखती है:

```yaml
# RuntimeClass is defined in the node.k8s.io API group
apiVersion: node.k8s.io/v1
kind: RuntimeClass
metadata:
  # The name the RuntimeClass will be referenced by.
  # RuntimeClass is a non-namespaced resource.
  name: myclass 
# The name of the corresponding CRI configuration
handler: myconfiguration 
```

The name of a RuntimeClass object must be a valid
[DNS subdomain name](/docs/concepts/overview/working-with-objects/names#dns-subdomain-names).

{{< note >}}
It is recommended that RuntimeClass write operations (create/update/patch/delete) be
restricted to the cluster administrator. This is typically the default. See
[Authorization Overview](/docs/reference/access-authn-authz/authorization/) for more details.
{{< /note >}}

किसी रनटाइमक्लास ऑब्जेक्ट का नाम मान्य होना आवश्यक है
[DNS उपडोमेन नाम] (/डॉक्स/कॉन्सेप्ट्स/ओवरव्यू/वर्किंग-विद-ऑब्जेक्ट्स/नाम#डीएनएस-सबडोमेन-नाम)।

{{< नोट >}}
यह अनुशंसा की जाती है कि रनटाइमक्लास लेखन संचालन (बनाएं / अपडेट / पैच / हटाएं)
क्लस्टर व्यवस्थापक तक ही सीमित. यह आमतौर पर डिफ़ॉल्ट है। देखना
[प्राधिकरण अवलोकन] (/दस्तावेज़/संदर्भ/एक्सेस-ऑथन-ऑथज़/प्राधिकरण/) अधिक जानकारी के लिए।
{{</नोट >}}

## Usage

Once RuntimeClasses are configured for the cluster, you can specify a
`runtimeClassName` in the Pod spec to use it. For example:

## उपयोग

क्लस्टर के लिए रनटाइमक्लास कॉन्फ़िगर किए जाने के बाद, आप एक निर्दिष्ट कर सकते हैं
इसका उपयोग करने के लिए पॉड स्पेक में 'रनटाइमक्लास नेम'। उदाहरण के लिए:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  runtimeClassName: myclass
  # ...
```

This will instruct the kubelet to use the named RuntimeClass to run this pod. If the named
RuntimeClass does not exist, or the CRI cannot run the corresponding handler, the pod will enter the
`Failed` terminal [phase](/docs/concepts/workloads/pods/pod-lifecycle/#pod-phase). Look for a
corresponding [event](/docs/tasks/debug/debug-application/debug-running-pod/) for an
error message.

यह कुबेलेट को इस पॉड को चलाने के लिए नामित रनटाइमक्लास का उपयोग करने का निर्देश देगा। यदि नाम दिया गया है
रनटाइमक्लास मौजूद नहीं है, या CRI संबंधित हैंडलर नहीं चला सकता है, पॉड प्रवेश करेगा
'असफल' टर्मिनल [चरण](/डॉक्स/अवधारणाएं/वर्कलोड/पॉड्स/पॉड-जीवनचक्र/#pod-चरण)। एक की तलाश करें
संबंधित [इवेंट](/docs/tasks/debug/debug-application/debug-रनिंग-pod/) के लिए
त्रुटि संदेश.

If no `runtimeClassName` is specified, the default RuntimeHandler will be used, which is equivalent
to the behavior when the RuntimeClass feature is disabled.

यदि कोई 'रनटाइमक्लास नाम' निर्दिष्ट नहीं है, तो डिफ़ॉल्ट रनटाइमहैंडलर का उपयोग किया जाएगा, जो बराबर है।
जब RuntimeClass सुविधा अक्षम की जाती है, तो व्यवहार के लिए.

### CRI Configuration

For more details on setting up CRI runtimes, see [CRI installation](/docs/setup/production-environment/container-runtimes/).
### CRI कॉन्फ़िगरेशन

CRI रनटाइम सेट करने के बारे में अधिक जानकारी के लिए, देखें [CRI स्थापना](/docs/setup/production-environment/कंटेनर-रनटाइम/)।

#### {{< glossary_tooltip term_id="containerd" >}}

Runtime handlers are configured through containerd's configuration at
`/etc/containerd/config.toml`. Valid handlers are configured under the runtimes section:

```
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.${HANDLER_NAME}]
```

See containerd's [config documentation](https://github.com/containerd/containerd/blob/main/docs/cri/config.md)
for more details:
#### {{< glossary_tooltip term_id="कंटेनर्ड" >}}

रनटाइम हैंडलर को कंटेनर के कॉन्फ़िगरेशन के माध्यम से कॉन्फ़िगर किया जाता है
'/etc/containerd/config.toml'. मान्य हैंडलर रनटाइम अनुभाग के अंतर्गत कॉन्फ़िगर किए गए हैं:

```
[प्लगइन्स। io.containerd.grpc.v1.cri".containerd.runtimes.${HANDLER_NAME}]
```

देखें कंटेनर्ड [कॉन्फ़िगरेशन दस्तावेज़](https://github.com/containerd/containerd/blob/main/docs/cri/config.md)
अधिक जानकारी के लिए:

#### {{< glossary_tooltip term_id="cri-o" >}}

Runtime handlers are configured through CRI-O's configuration at `/etc/crio/crio.conf`. Valid
handlers are configured under the
[crio.runtime table](https://github.com/cri-o/cri-o/blob/master/docs/crio.conf.5.md#crioruntime-table):

रनटाइम हैंडलर को सीआरआई-ओ के कॉन्फ़िगरेशन के माध्यम से '/etc/crio/crio.conf' पर कॉन्फ़िगर किया जाता है। वैध
हैंडलर के तहत कॉन्फ़िगर किए गए हैं
[crio.runtime तालिका] (https://github.com/cri-o/cri-o/blob/master/docs/crio.conf.5.md#crioruntime-table):

```
[crio.runtime.runtimes.${HANDLER_NAME}]
  runtime_path = "${PATH_TO_BINARY}"
```

See CRI-O's [config documentation](https://github.com/cri-o/cri-o/blob/master/docs/crio.conf.5.md) for more details.
अधिक जानकारी के लिए CRI-O [config documentation](https://github.com/cri-o/cri-o/blob/master/docs/crio.conf.5.md) देखें।

## Scheduling ## शेड्यूलिंग

{{< feature-state for_k8s_version="v1.16" state="beta" >}}

By specifying the `scheduling` field for a RuntimeClass, you can set constraints to
ensure that Pods running with this RuntimeClass are scheduled to nodes that support it.
If `scheduling` is not set, this RuntimeClass is assumed to be supported by all nodes.
रनटाइमक्लास के लिए 'शेड्यूलिंग' फ़ील्ड निर्दिष्ट करके, आप इस पर प्रतिबंध सेट कर सकते हैं
सुनिश्चित करें कि इस रनटाइमक्लास के साथ चलने वाले पॉड्स उन नोड्स पर शेड्यूल किए गए हैं जो इसका समर्थन करते हैं।
यदि 'शेड्यूलिंग' सेट नहीं है, तो यह रनटाइमक्लास सभी नोड्स द्वारा समर्थित माना जाता है।

To ensure pods land on nodes supporting a specific RuntimeClass, that set of nodes should have a
common label which is then selected by the `runtimeclass.scheduling.nodeSelector` field. The
RuntimeClass's nodeSelector is merged with the pod's nodeSelector in admission, effectively taking
the intersection of the set of nodes selected by each. If there is a conflict, the pod will be
rejected.
यह सुनिश्चित करने के लिए कि पॉड्स एक विशिष्ट रनटाइमक्लास का समर्थन करने वाले नोड्स पर उतरते हैं, नोड्स के उस सेट में
सामान्य लेबल जिसे तब 'runtimeclass.शेड्यूलिंग.नोड सेलेक्टर' फ़ील्ड द्वारा चुना जाता है। वही
रनटाइमक्लास के नोडसेलेक्टर को पॉड के नोडसेलेक्टर के साथ विलय कर दिया जाता है, प्रभावी रूप से
प्रत्येक द्वारा चुने गए नोड्स के सेट का प्रतिच्छेदन। यदि कोई संघर्ष होता है, तो पॉड होगा
खारिज कर दिया।

If the supported nodes are tainted to prevent other RuntimeClass pods from running on the node, you
can add `tolerations` to the RuntimeClass. As with the `nodeSelector`, the tolerations are merged
with the pod's tolerations in admission, effectively taking the union of the set of nodes tolerated
by each.
यदि समर्थित नोड्स अन्य रनटाइमक्लास पॉड्स को नोड पर चलने से रोकने के लिए दूषित हैं, तो आप
रनटाइमक्लास में 'सहनशीलता' जोड़ सकते हैं। 'नोड सेलेक्टर के साथ, सहनशीलता का विलय कर दिया जाता है।
प्रवेश में पॉड की सहनशीलता के साथ, प्रभावी रूप से नोड्स के सेट के संघ को सहन करना।
प्रत्येक के द्वारा।

To learn more about configuring the node selector and tolerations, see
[Assigning Pods to Nodes](/docs/concepts/scheduling-eviction/assign-pod-node/).
नोड चयनकर्ता और सहनशीलता कॉन्फ़िगर करने के बारे में अधिक जानने के लिए, देखें
[नोड्स को पॉड्स असाइन करना] (/डॉक्स/कॉन्सेप्ट्स/शेड्यूलिंग-एविक्शन/असाइन-पॉड-नोड/)।

### Pod Overhead ### पॉड ओवरहेड

{{< feature-state for_k8s_version="v1.24" state="stable" >}}

You can specify _overhead_ resources that are associated with running a Pod. Declaring overhead allows
the cluster (including the scheduler) to account for it when making decisions about Pods and resources.

Pod overhead is defined in RuntimeClass through the `overhead` field. Through the use of this field,
you can specify the overhead of running pods utilizing this RuntimeClass and ensure these overheads
are accounted for in Kubernetes.
आप _overhead_ संसाधन निर्दिष्ट कर सकते हैं जो पॉड चलाने से संबद्ध हैं. ओवरहेड घोषित करने की अनुमति
पॉड्स और संसाधनों के बारे में निर्णय लेते समय क्लस्टर (शेड्यूलर सहित) को ध्यान में रखना।

पॉड ओवरहेड को रनटाइमक्लास में 'ओवरहेड' फ़ील्ड के माध्यम से परिभाषित किया गया है। इस क्षेत्र के उपयोग के माध्यम से,
आप इस रनटाइमक्लास का उपयोग करके रनिंग पॉड्स के ओवरहेड को निर्दिष्ट कर सकते हैं और इन ओवरहेड्स को सुनिश्चित कर सकते हैं।
कुबेरनेट्स में इनका हिसाब रखा जाता है।

## {{% heading "whatsnext" %}}

- [RuntimeClass Design](https://github.com/kubernetes/enhancements/blob/master/keps/sig-node/585-runtime-class/README.md)
- [RuntimeClass Scheduling Design](https://github.com/kubernetes/enhancements/blob/master/keps/sig-node/585-runtime-class/README.md#runtimeclass-scheduling)
- Read about the [Pod Overhead](/docs/concepts/scheduling-eviction/pod-overhead/) concept
- [PodOverhead Feature Design](https://github.com/kubernetes/enhancements/tree/master/keps/sig-node/688-pod-overhead)
- - [रनटाइमक्लास डिज़ाइन](https://github.com/kubernetes/enhancements/blob/master/keps/sig-नोड/585-रनटाइम-क्लास/README.md)
- [रनटाइमक्लास शेड्यूलिंग डिज़ाइन](https://github.com/kubernetes/enhancements/blob/master/keps/sig-नोड/585-रनटाइम-क्लास/README.md#रनटाइमक्लास-शेड्यूलिंग)
- - [पॉड ओवरहेड] (/डॉक्स/कॉन्सेप्ट्स/शेड्यूलिंग-एविक्शन/पॉड-ओवरहेड/) कॉन्सेप्ट के बारे में पढ़ें
- [पॉडओवरहेड फीचर डिज़ाइन](https://github.com/kubernetes/enhancements/tree/master/keps/sig-नोड/688-पॉड-ओवरहेड)
