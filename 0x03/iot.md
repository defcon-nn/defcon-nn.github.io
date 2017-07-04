---
layout: talk
meetup: meetup_14_05_2017
speaker: 2
---

## Слайды

[Скачать PDF](./soa.pdf)

<script async class="speakerdeck-embed" data-id="ba81333dc304440ab3811b51fb1b00d4" data-ratio="1.33333333333333" src="//speakerdeck.com/assets/embed.js"></script>

В докладе рассматривается методология построения безопасных систем в применении к созданию приложений для "интернета вещей". Рассказывается, что такое модель угроз, как она встраивается в процесс разработки программного обеспечения (SDLC). На примере приложения для голосового управления иллюстрируется применение принипа разделения обязанностей и принципа наименьших привилегий. Рассматриваются практические аспекты создания приложения с сервис-ориентированной архитектурой в окружении Yocto Linux - использование DBus IPC, выбор подходящих безопасных языков программирования (среди Go, Rust, Python, Node.js, Java). Затрагиваются вопросы изоляции уязвимого кода, обрабатывающего недоверенные входные данные.

Доклад делается на основе практического опыта автора, принимавшего участие в создании коммерческой системы умного дома в качестве архитектора, разработчика и специалиста по безопасности на этапе проектирования и прототипирования. В результате создан работающий прототип, предложена архитектура для продуктовой реализации, заложены основы этой реализации.