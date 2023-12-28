# Создание кластера {{ dataproc-name }}

Для создания кластера {{ dataproc-name }} пользователю должны быть назначены роли `editor` и `dataproc.agent`. Подробнее см. в [описании ролей](../security/index.md#roles-list).


## Настройте сеть {#setup-network}

Настройте доступ в интернет из подсети, к которой будет подключен подкластер с хостом-мастером, например при помощи [NAT-шлюза](../../vpc/operations/create-nat-gateway.md). Это необходимо, чтобы подкластер мог взаимодействовать c сервисами {{ yandex-cloud }} или хостами в других сетях.

## Настройте группы безопасности {#change-security-groups}

{% note warning %}

Группы безопасности необходимо создать и настроить перед созданием кластера. Если в выбранных группах безопасности не будет необходимых правил, {{ yandex-cloud }} заблокирует создание кластера.

{% endnote %}

1. [Создайте](../../vpc/operations/security-group-create.md) одну или несколько групп безопасности для служебного трафика кластера.
1. [Добавьте правила](../../vpc/operations/security-group-add-rule.md):

    * По одному правилу для входящего и исходящего служебного трафика:

        * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}**  — `{{ port-any }}`.
        * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}** — `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_any }}` (`Any`).
        * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-source }}**/**{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-destination }}**— `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-destination-sg }}`.
        * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-sg-type }}** — `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-sg-type-self }}` (`Self`).

    * Отдельное правило для исходящего HTTPS-трафика. Это позволит использовать [бакеты {{ objstorage-full-name }}](../../storage/concepts/bucket.md), [UI Proxy](../concepts/interfaces.md) и [автоматическое масштабирование](../concepts/autoscaling.md) кластеров.

        Вы можете настроить это правило одним из двух способов:

        {% list tabs %}

        - На все адреса

            * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}** — `{{ port-https }}`.
            * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}** — `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_tcp }}`.
            * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-destination }}** — `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-destination-cidr }}`.
            * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-cidr-blocks }}** — `0.0.0.0/0`.

        - На адреса, используемые {{ yandex-cloud }}

            * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}** — `{{ port-https }}`.
            * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}** — `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_tcp }}`.
            * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-destination }}** — `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-destination-cidr }}`.
            * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-cidr-blocks }}**:
                * `84.201.181.26/32` — получение статуса кластера, запуск заданий, UI Proxy.
                * `158.160.59.216/32` — мониторинг состояния кластера, автомасштабирование.
                * `213.180.193.243/32` — доступ к {{ objstorage-name }}.

        {% endlist %}

   * Правило, разрешающее доступ к NTP-серверам для синхронизации времени.

Если планируется использовать несколько групп безопасности для кластера, разрешите весь трафик между этими группами.

{% note info %}

Вы можете задать более детальные правила для групп безопасности, например, разрешающие трафик только в определенных подсетях.

Группы безопасности должны быть корректно настроены для всех подсетей, в которых будут размещены хосты кластера.

{% endnote %}

Настройку групп безопасности для [подключения к хостам кластера](connect.md) через промежуточную ВМ и для [подключения к {{ metastore-name }}](./metastore/dataproc-connect.md) можно выполнить после создания кластера.


## Создайте кластер {#create}

Кластер должен состоять из подкластера с хостом-мастером и как минимум из одного подкластера для хранения или обработки данных.

{% list tabs %}

- Консоль управления

  1. В [консоли управления]({{ link-console-main }}) выберите каталог, в котором нужно создать кластер.

  1. Нажмите кнопку **{{ ui-key.yacloud.iam.folder.dashboard.button_add }}** и выберите ![image](../../_assets/data-proc/data-proc.svg) **{{ ui-key.yacloud.iam.folder.dashboard.value_data-proc }}** в выпадающем списке.

  1. Введите имя кластера в поле **{{ ui-key.yacloud.mdb.forms.base_field_name }}**. Требования к имени:

     * должно быть уникальным в рамках каталога;

     {% include [name-format.md](../../_includes/name-format.md) %}

  1. Выберите подходящую [версию образа](../concepts/environment.md) и сервисы, которые вы хотите использовать в кластере.

     {% include [note-light-weight-cluster](../../_includes/data-proc/note-light-weight-cluster.md) %}

     {% note tip %}

     Чтобы использовать самую свежую версию образа, укажите значение `2.0`.

     {% endnote %}

  1. Вставьте в поле **{{ ui-key.yacloud.mdb.forms.config_field_public-keys }}** публичную часть вашего SSH-ключа. Как сгенерировать и использовать SSH-ключи, читайте в [документации {{ compute-full-name }}](../../compute/operations/vm-connect/ssh.md).
  1. Выберите или создайте [сервисный аккаунт](../../iam/concepts/users/service-accounts.md), которому нужно разрешить доступ к кластеру. Сервисному аккаунту кластера должна быть [назначена роль](../security/index.md#grant-role) `dataproc.agent`.
  1. Выберите зону доступности для кластера.
  1. При необходимости задайте [свойства компонентов кластера, заданий и среды окружения](../concepts/settings-list.md).
  1. При необходимости укажите пользовательские [скрипты инициализации](../concepts/init-action.md) хостов кластера. Для каждого скрипта укажите:

      * **{{ ui-key.yacloud.mdb.forms.field_initialization-action-uri }}** — ссылка на скрипт инициализации в схеме `https://`, `http://`, `hdfs://` или `s3a://`.
      * (Опционально) **{{ ui-key.yacloud.mdb.forms.field_initialization-action-timeout }}** —  таймаут (в секундах) выполнения скрипта. Скрипт инициализации, выполняющийся дольше указанного времени, будет прерван.
      * (Опционально) **{{ ui-key.yacloud.mdb.forms.field_initialization-action-args }}** — заключенные в квадратные скобки `[]` и разделенные запятыми аргументы, с которыми должен быть выполнен скрипт инициализации, например:

          ```text
          ["arg1","arg2",...,"argN"]
          ```

  1. Выберите имя бакета в {{ objstorage-full-name }}, в котором будут храниться зависимости заданий и результаты их выполнения.
  1. Выберите сеть для кластера.
  1. Выберите группы безопасности, в которых имеются необходимые разрешения.

      {% note warning %}

      При создании кластера проверяются настройки групп безопасности. Если функционирование кластера с этими настройками невозможно, то будет выведено предупреждение. Пример работающих настроек приведен [выше](#change-security-groups).

      {% endnote %}

  1. Включите опцию **{{ ui-key.yacloud.mdb.forms.config_field_ui_proxy }}**, чтобы получить доступ к [веб-интерфейсам компонентов](../concepts/interfaces.md) {{ dataproc-name }}.

  
  1. Логи кластера сохраняются в сервисе [{{ cloud-logging-full-name }}](../../logging/). Выберите нужную лог-группу из списка или [создайте новую](../../logging/operations/create-group.md).

      Для работы этой функции [назначьте сервисному аккаунту кластера](../../iam/operations/roles/grant.md#access-to-sa) роль `logging.writer`. Подробнее см. в [документации {{ cloud-logging-full-name }}](../../logging/security/index.md).


  1. Настройте подкластеры: не больше одного подкластера с хостом-мастером (обозначается как **Мастер**), и подкластеры для хранения или обработки данных.

      Роли подкластеров для хранения и обработки данных различаются тем, что на подкластерах для хранения данных можно разворачивать компоненты для хранения, а для обработки — компоненты для вычислений. Хранилище на подкластере для обработки данных предназначено только для временного хранения обрабатываемых файлов.

      Для каждого подкластера можно настроить:

      * Количество хостов.
      * [Класс хостов](../concepts/instance-types.md) — платформа и вычислительные ресурсы, доступные хосту.
      * Размер и тип [хранилища](../concepts/storage.md).
      * Подсеть сети, в которой расположен кластер.

          В подсети для подкластера с хостом-мастером нужно [настроить NAT-шлюз](../../vpc/operations/create-nat-gateway.md). Подробнее см. в разделе [{#T}](#setup-network).

      * Для доступа к хостам подкластера из интернета выберите опцию **{{ ui-key.yacloud.mdb.forms.field_assign-public-ip }}**. В этом случае подключаться к хостам подкластера можно только с использованием SSL-соединения. Подробнее см. в разделе [{#T}](connect.md).

          {% note warning %}

          После создания кластера невозможно запросить или отключить публичный доступ к подкластеру. Однако подкластер для обработки данных можно удалить и создать заново с нужной настройкой публичного доступа.

          {% endnote %}

  1. В подкластерах для обработки данных можно задать параметры [автоматического масштабирования](../concepts/autoscaling.md).

     {% include [note-info-service-account-roles](../../_includes/data-proc/service-account-roles.md) %}

     1. В блоке **{{ ui-key.yacloud.mdb.forms.label_create-subcluster }}** нажмите кнопку **{{ ui-key.yacloud.mdb.forms.button_configure }}**.
     1. В поле **{{ ui-key.yacloud.mdb.forms.base_field_roles }}** выберите `COMPUTENODE`.
     1. В блоке **{{ ui-key.yacloud.mdb.forms.section_scaling }}** включите настройку **{{ ui-key.yacloud.mdb.forms.label_autoscaling-activated }}**.
     1. Задайте параметры автоматического масштабирования.
     1. По умолчанию в качестве метрики для автоматического масштабирования используется `yarn.cluster.containersPending`. Чтобы включить масштабирование на основе загрузки CPU, выключите настройку **{{ ui-key.yacloud.compute.groups.create.field_default-utilization-target }}** и укажите целевой уровень загрузки CPU.
     1. Нажмите кнопку **{{ ui-key.yacloud.mdb.forms.button_add-subcluster }}**.

  1. При необходимости задайте дополнительные настройки кластера:

      **{{ ui-key.yacloud.mdb.forms.label_deletion-protection }}** — управляет защитой кластера от непреднамеренного удаления пользователем.

      Включенная защита не помешает подключиться к кластеру вручную и удалить данные.

  1. Нажмите кнопку **{{ ui-key.yacloud.mdb.forms.button_create }}**.

- CLI

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    Чтобы создать кластер:

    
    1. Проверьте, есть ли в каталоге подсети для хостов кластера:

        ```bash
        yc vpc subnet list
        ```

        Если ни одной подсети в каталоге нет, [создайте нужные подсети](../../vpc/operations/subnet-create.md) в сервисе {{ vpc-full-name }}.


    1. Посмотрите описание команды CLI для создания кластера:

        ```bash
        {{ yc-dp }} cluster create --help
        ```

    1. Укажите параметры кластера в команде создания (в примере приведены не все доступные параметры):

        
        ```bash
        {{ yc-dp }} cluster create <имя_кластера> \
           --bucket=<имя_бакета> \
           --zone=<зона_доступности> \
           --service-account-name=<имя_сервисного_аккаунта> \
           --version=<версия_образа> \
           --services=<список_компонентов> \
           --ssh-public-keys-file=<путь_к_открытому_SSH-ключу> \
           --subcluster name=<имя_подкластера_с_хостом-мастером>,`
                       `role=masternode,`
                       `resource-preset=<класс_хоста>,`
                       `disk-type=<тип_хранилища>,`
                       `disk-size=<размер_хранилища_ГБ>,`
                       `subnet-name=<имя_подсети>,`
                       `assign-public-ip=<публичный_доступ_к_хосту_подкластера> \
           --subcluster name=<имя_подкластера_для_хранения_данных>,`
                       `role=datanode,`
                       `resource-preset=<класс_хоста>,`
                       `disk-type=<тип_хранилища>,`
                       `disk-size=<размер_хранилища_ГБ>,`
                       `subnet-name=<имя_подсети>,`
                       `hosts-count=<количество_хостов>,`
                       `assign-public-ip=<публичный_доступ_к_хосту_подкластера> \
           --deletion-protection=<защита_от_удаления_кластера> \
           --ui-proxy=<доступ_к_веб-интерфейсам_компонентов> \
           --log-group-id=<идентификатор_лог-группы> \
           --security-group-ids=<список_идентификаторов_групп_безопасности>
        ```



        {% note info %}

        Имя кластера должно быть уникальным в рамках каталога. Может содержать латинские буквы, цифры, дефис и нижнее подчеркивание. Максимальная длина имени 63 символа.

        {% endnote %}

        Где:

        * `--bucket` — имя бакета в {{ objstorage-full-name }}, в котором будут храниться зависимости заданий и результаты их выполнения. Сервисный аккаунт кластера должен иметь разрешение `READ и WRITE` для этого бакета.
        * `--zone` — [зона доступности](../../overview/concepts/geo-scope.md), в которой должны быть размещены хосты кластера.
        * `--service-account-name` — имя [сервисного аккаунта кластера](../../iam/concepts/users/service-accounts.md). Сервисному аккаунту кластера должна быть [назначена роль](../security/index.md#grant-role) `dataproc.agent`.
        * `--version` — [версия образа](../concepts/environment.md).

            {% include [note-light-weight-cluster](../../_includes/data-proc/note-light-weight-cluster.md) %}

            {% note tip %}

            Чтобы использовать самую свежую версию образа, укажите значение `2.0` в параметре `--version`.

            {% endnote %}

        * `--services` — список [компонентов](../concepts/environment.md), которые вы хотите использовать в кластере. Если не указать этот параметр, будет использоваться набор по умолчанию: `hdfs`, `yarn`, `mapreduce`, `tez`, `spark`.
        * `--ssh-public-keys-file` — полный путь к файлу с публичной частью SSH-ключа, который будет использоваться для доступа к хостам кластера. Как создать и использовать SSH-ключи, читайте в [документации {{ compute-full-name }}](../../compute/operations/vm-connect/ssh.md).
        * `--subcluster` — параметры подкластеров:
            * `name` — имя подкластера.
            * `role` — роль подкластера: `masternode`, `datanode` или `computenode`.
            * `resource-preset` — [класс хостов](../concepts/instance-types.md).
            * `disk-type` — [тип хранилища](../concepts/storage.md): `network-ssd`, `network-hdd` или `network-ssd-nonreplicated`.
            * `disk-size` — размер хранилища в гигабайтах.
            * `subnet-name` — [имя подсети](../../vpc/concepts/network.md#subnet).
            * `hosts-count` — количество хостов подкластеров для хранения или обработки данных. Минимальное значение — `1`, максимальное — `32`.
            * `assign-public-ip` — доступ к хостам подкластера из интернета. Может принимать значения `true` или `false`. Если доступ включен, подключаться к кластеру можно только с использованием SSL-соединения. Подробнее см. в разделе [{#T}](connect.md).

                {% note warning %}

                После создания кластера невозможно запросить или отключить публичный доступ к подкластеру. Однако подкластер для обработки данных можно удалить и создать заново с нужной настройкой публичного доступа.

                {% endnote %}

        * `--deletion-protection` — защита от удаления кластера. Может принимать значения `true` или `false`.

            {% include [Deletion protection limits](../../_includes/mdb/deletion-protection-limits-data.md) %}

        * `--ui-proxy` — доступ к [веб-интерфейсам компонентов](../concepts/interfaces.md) {{ dataproc-name }}. Может принимать значения `true` или `false`.

        
        * `--log-group-id` — [идентификатор лог-группы](../concepts/logs.md).


        * `--security-group-ids` — список идентификаторов [групп безопасности](../../vpc/concepts/security-groups.md).

        Чтобы создать кластер, состоящих из нескольких подкластеров для хранения или обработки данных, передайте необходимое количество аргументов `--subcluster` в команде создания кластера:

        ```bash
        {{ yc-dp }} cluster create <имя_кластера> \
           ...
           --subcluster <параметры_подкластера> \
           --subcluster <параметры_подкластера> \
           ...
        ```

    1. Чтобы включить [автоматическое масштабирование](../concepts/autoscaling.md) в подкластерах для обработки данных, задайте параметры:

        ```bash
        {{ yc-dp }} cluster create <имя_кластера> \
           ...
           --subcluster name=<имя_подкластера>,`
                       `role=computenode`
                       `...`
                       `hosts-count=<минимальное_количество_хостов>`
                       `max-hosts-count=<максимальное_количество_хостов>,`
                       `preemptible=<использование_прерываемых_ВМ>,`
                       `warmup-duration=<время_на_разогрев_ВМ>,`
                       `stabilization-duration=<период_стабилизации>,`
                       `measurement-duration=<промежуток_измерения_нагрузки>,`
                       `cpu-utilization-target=<целевой_уровень_загрузки_CPU>,`
                       `autoscaling-decommission-timeout=<таймаут_декомиссии>
        ```

        Где:

        * `hosts-count` — минимальное количество хостов (виртуальных машин) в подкластере. Минимальное значение — `1`, максимальное — `32`.
        * `max-hosts-count` — максимальное количество хостов (виртуальных машин) в подкластере. Минимальное значение — `1`, максимальное — `100`.
        * `preemptible` — использование [прерываемых ВМ](../../compute/concepts/preemptible-vm.md). Может принимать значения `true` или `false`.
        * `warmup-duration` — время в секундах на разогрев ВМ, в формате `<значение>s`. Минимальное значение — `0s`, максимальное — `600s` (10 минут).
        * `stabilization-duration` — период в секундах, в течение которого требуемое количество ВМ не может быть снижено, в формате `<значение>s`. Минимальное значение — `60s` (1 минута), максимальное — `1800s` (30 минут).
        * `measurement-duration` — период в секундах, за который замеры нагрузки усредняются для каждой ВМ, в формате `<значение>s`. Минимальное значение — `60s` (1 минута), максимальное — `600s` (10 минут).
        * `cpu-utilization-target` — целевой уровень загрузки CPU, в процентах. Используйте эту настройку, чтобы включить [масштабирование](../concepts/autoscaling.md) на основе загрузки CPU, иначе в качестве метрики будет использоваться `yarn.cluster.containersPending` (на основе количества ожидающих задания ресурсов). Минимальное значение — `10`, максимальное — `100`.
        * `autoscaling-decommission-timeout` — [таймаут декомиссии](../concepts/decommission.md) в секундах. Минимальное значение — `0`, максимальное — `86400` (сутки).

        {% include [note-info-service-account-roles](../../_includes/data-proc/service-account-roles.md) %}

    
    1. Чтобы создать кластер, размещенный на [группах выделенных хостов](../../compute/concepts/dedicated-host.md), укажите через запятую их идентификаторы в параметре `--host-group-ids`:

        ```bash
        {{ yc-dp }} cluster create <имя_кластера> \
           ...
           --host-group-ids=<идентификаторы_групп_выделенных_хостов>
        ```

        {% include [Dedicated hosts note](../../_includes/data-proc/note-dedicated-hosts.md) %}


    1. Чтобы настроить хосты кластера с помощью [скриптов инициализации](../concepts/init-action.md), укажите их в одном или нескольких параметрах `--initialization-action`:

        ```bash
        {{ yc-dp }} cluster create <имя_кластера> \
           ...
           --initialization-action uri=<URI_скрипта_инициализации>,`
                                  `timeout=<таймаут_выполнения_скрипта>,`
                                  `args=["arg1","arg2","arg3",...]
        ```

        Где:

        * `uri` — ссылка на скрипт инициализации в схеме `https://`, `http://`, `hdfs://` или `s3a://`.
        * (опционально) `timeout` — таймаут выполнения скрипта, в секундах. Скрипт инициализации, выполняющийся дольше указанного времени, будет прерван.
        * (опционально) `args` — разделенные запятыми аргументы, с которыми должен быть выполнен скрипт инициализации.

- {{ TF }}

    {% include [terraform-definition](../../_tutorials/terraform-definition.md) %}

  Чтобы создать кластер:

    1. В командной строке перейдите в каталог, в котором будут расположены конфигурационные файлы {{ TF }} с планом инфраструктуры. Если такой директории нет — создайте ее.

    
    1. {% include [terraform-install](../../_includes/terraform-install.md) %}

    1. Создайте конфигурационный файл с описанием [облачной сети](../../vpc/concepts/network.md#network) и [подсетей](../../vpc/concepts/network.md#subnet).

       Кластер размещается в облачной сети. Если подходящая сеть у вас уже есть, описывать ее повторно не нужно.

       Хосты кластера размещаются в подсетях выбранной облачной сети. Если подходящие подсети у вас уже есть, описывать их повторно не нужно.

       Пример структуры конфигурационного файла, в котором описывается облачная сеть с одной подсетью:

       ```hcl
       resource "yandex_vpc_network" "test_network" { 
         name = "<имя_сети>" 
       }

       resource "yandex_vpc_subnet" "test_subnet" {
         name           = "<имя_подсети>"
         zone           = "<зона_доступности>"
         network_id     = yandex_vpc_network.test_network.id
         v4_cidr_blocks = ["<подсеть>"]
       }
       ```


    1. Создайте конфигурационный файл с описанием [сервисного аккаунта](../../iam/concepts/users/service-accounts.md), которому нужно разрешить доступ к кластеру, а также [статического ключа](../../iam/concepts/authorization/access-key.md) и [бакета {{ objstorage-full-name }}](../../storage/concepts/bucket.md) для хранения заданий и результатов.

       ```hcl
       resource "yandex_iam_service_account" "data_proc_sa" {
         name        = "<имя_сервисного_аккаунта>"
         description = "<описание_сервисного_аккаунта>"
       }

       resource "yandex_resourcemanager_folder_iam_member" "dataproc" {
         folder_id = "<идентификатор_каталога>"
         role      = "dataproc.agent"
         member    = "serviceAccount:${yandex_iam_service_account.data_proc_sa.id}"
       }

       resource "yandex_resourcemanager_folder_iam_member" "bucket-creator" {
         folder_id = "<идентификатор_каталога>"
         role      = "dataproc.editor"
         member    = "serviceAccount:${yandex_iam_service_account.data_proc_sa.id}"
       }

       resource "yandex_iam_service_account_static_access_key" "sa_static_key" {
         service_account_id = yandex_iam_service_account.data_proc_sa.id
       }

       resource "yandex_storage_bucket" "data_bucket" {
         depends_on = [
           yandex_resourcemanager_folder_iam_member.bucket-creator
         ]

         bucket     = "<имя_бакета>"
         access_key = yandex_iam_service_account_static_access_key.sa_static_key.access_key
         secret_key = yandex_iam_service_account_static_access_key.sa_static_key.secret_key
       }
       ```

    1. Создайте конфигурационный файл с описанием кластера и его подкластеров.

       При необходимости здесь же можно задать [свойства компонентов кластера, заданий и среды окружения](../concepts/settings-list.md).

       Пример структуры конфигурационного файла, в котором описывается кластер из одного подкластера с хостом-мастером, одного подкластера для хранения данных и одного подкластера для обработки данных:

       ```hcl
       resource "yandex_dataproc_cluster" "data_cluster" {
         bucket              = "<имя_бакета>"
         name                = "<имя_кластера>"
         description         = "<описание_кластера>"
         service_account_id  = yandex_iam_service_account.data_proc_sa.id
         zone_id             = "<зона_доступности>"
         security_group_ids  = ["<список_идентификаторов_групп_безопасности>"]
         deletion_protection = <защита_от_удаления_кластера>

         cluster_config {
           version_id = "<версия_образа>"

           hadoop {
             services   = ["<список_компонентов>"]
             # пример списка: ["HDFS", "YARN", "SPARK", "TEZ", "MAPREDUCE", "HIVE"]
             properties = {
               "<свойство_компонента>" = <значение>
               ...
             }
             ssh_public_keys = [
               file("${file("<путь_к_открытому_SSH-ключу>")}")
             ]
           }

           subcluster_spec {
             name = "<имя_подкластера_с_хостом-мастером>"
             role = "MASTERNODE"
             resources {
               resource_preset_id = "<класс_хоста>"
               disk_type_id       = "<тип_хранилища>"
               disk_size          = <объем_хранилища_ГБ>
             }
             subnet_id   = yandex_vpc_subnet.test_subnet.id
             hosts_count = 1
           }

           subcluster_spec {
             name = "<имя_подкластера_для_хранения_данных>"
             role = "DATANODE"
             resources {
               resource_preset_id = "<класс_хоста>"
               disk_type_id       = "<тип_хранилища>"
               disk_size          = <объем_хранилища_ГБ>
             }
             subnet_id   = yandex_vpc_subnet.test_subnet.id
             hosts_count = <число_хостов_в_подкластере>
           }

           subcluster_spec {
             name = "<имя_подкластера_для_обработки_данных>"
             role = "COMPUTENODE"
             resources {
               resource_preset_id = "<класс_хоста>"
               disk_type_id       = "<тип_хранилища>"
               disk_size          = <объем_хранилища_ГБ>
             }
             subnet_id   = yandex_vpc_subnet.test_subnet.id
             hosts_count = <число_хостов_в_подкластере>
           }
         }
       }
       ```

       Где `deletion_protection` — защита от удаления кластера. Может принимать значения `true` или `false`.

       {% include [Ограничения защиты от удаления](../../_includes/mdb/deletion-protection-limits-db.md) %}

       {% include [note-light-weight-cluster](../../_includes/data-proc/note-light-weight-cluster.md) %}

       {% note tip %}

       Чтобы использовать самую свежую версию образа, укажите значение `2.0` в параметре `version_id`.

       {% endnote %}

       Чтобы получить доступ к [веб-интерфейсам компонентов](../concepts/interfaces.md) {{ dataproc-name }}, добавьте в описание кластера поле `ui_proxy` с значением `true`:

       ```hcl
       resource "yandex_dataproc_cluster" "data_cluster" {
         ...
         ui_proxy = true
         ...
       }
       ```

       Чтобы задать параметры [автоматического масштабирования](../concepts/autoscaling.md) в подкластерах для обработки данных, добавьте в описание соответствующего подкластера `subcluster_spec` блок `autoscaling_config` с нужными вам настройками:

       ```hcl
       subcluster_spec {
         name = "<имя_подкластера>"
         role = "COMPUTENODE"
         ...
         autoscaling_config {
           max_hosts_count        = <максимальное_количество_ВМ_в_группе>
           measurement_duration   = <промежуток_измерения_нагрузки>
           warmup_duration        = <время_на_разогрев>
           stabilization_duration = <период_стабилизации>
           preemptible            = <использование_прерываемых_ВМ>
           cpu_utilization_target = <целевой_уровень_загрузки_vCPU>
           decommission_timeout   = <таймаут_декомиссии>
         }
       }
       ```

        Где:

        * `max_hosts_count` — максимальное количество хостов (виртуальных машин) в подкластере. Минимальное значение — `1`, максимальное — `100`.
        * `measurement_duration` — период в секундах, за который замеры нагрузки усредняются для каждой ВМ, в формате `<значение>s`. Минимальное значение — `60s` (1 минута), максимальное — `600s` (10 минут).
        * `warmup_duration` — время в секундах на разогрев ВМ, в формате `<значение>s`. Минимальное значение — `0s`, максимальное — `600s` (10 минут).
        * `stabilization_duration` — период в секундах, в течение которого требуемое количество ВМ не может быть снижено, в формате `<значение>s`. Минимальное значение — `60s` (1 минута), максимальное — `1800s` (30 минут).
        * `preemptible` — использование [прерываемых ВМ](../../compute/concepts/preemptible-vm.md). Может принимать значения `true` или `false`.
        * `cpu_utilization_target` — целевой уровень загрузки CPU, в процентах. Используйте эту настройку, чтобы включить [масштабирование](../concepts/autoscaling.md) на основе загрузки CPU, иначе в качестве метрики будет использоваться `yarn.cluster.containersPending` (на основе количества ожидающих задания ресурсов). Минимальное значение — `10`, максимальное — `100`.
        * `decommission_timeout` — [таймаут декомиссии](../concepts/decommission.md) в секундах. Минимальное значение — `0`, максимальное — `86400` (сутки).

       Более подробную информацию о ресурсах, которые вы можете создать с помощью {{ TF }}, см. в [документации провайдера]({{ tf-provider-resources-link }}/dataproc_cluster).

    1. Проверьте корректность файлов конфигурации {{ TF }}:

       {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Создайте кластер:

       {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

       {% include [explore-resources](../../_includes/mdb/terraform/explore-resources.md) %}

- API

    Чтобы создать кластер, воспользуйтесь методом API [create](../api-ref/Cluster/create) и передайте в запросе:

    * Идентификатор каталога, в котором должен быть размещен кластера, в параметре `folderId`.
    * Имя кластера в параметре `name`.
    * Конфигурацию кластера в параметре `configSpec`, в том числе:
        * [Версию образа](../concepts/environment.md) в параметре `configSpec.versionId`.

            {% include [note-light-weight-cluster](../../_includes/data-proc/note-light-weight-cluster.md) %}

            {% note tip %}

            Чтобы использовать самую свежую версию образа, укажите значение `2.0`.

            {% endnote %}

        * Список компонентов в параметре `configSpec.hadoop.services`.
        * Публичную часть SSH-ключа в параметре `configSpec.hadoop.sshPublicKeys`.
        * Настройки подкластеров в параметре `configSpec.subclustersSpec`.
    * Зону доступности кластера в параметре `zoneId`.
    * Идентификатор сервисного аккаунта кластера в параметре `serviceAccountId`.
    * Имя бакета в параметре `bucket`.
    * Идентификаторы групп безопасности кластера в параметре `hostGroupIds`.
    * Настройки защиты от удаления кластера в параметре `deletionProtection`.

        {% include [Deletion protection limits](../../_includes/mdb/deletion-protection-limits-data.md) %}

    Чтобы назначить публичный IP-адрес всем хостам подкластера, передайте значение `true` в параметре `configSpec.subclustersSpec.assignPublicIp`.

    
    Чтобы создать кластер, размещенный на [группах выделенных хостов](../../compute/concepts/dedicated-host.md), передайте список их идентификаторов в параметре `hostGroupIds`.

    {% include [Dedicated hosts note](../../_includes/data-proc/note-dedicated-hosts.md) %}


    Чтобы настроить хосты кластера с помощью [скриптов инициализации](../concepts/init-action.md), укажите их в одном или нескольких параметрах `configSpec.hadoop.initializationActions`.

{% endlist %}

После того как кластер перейдет в статус **Running**, вы можете [подключиться](connect.md) к хостам подкластеров с помощью указанного SSH-ключа.

## Пример {#example}

### Создание легковесного кластера для заданий Spark и PySpark {#creating-a-light-weight-cluster}

{% list tabs %}

- CLI

    Создайте кластер {{ dataproc-name }} для выполнения заданий Spark без HDFS и подкластеров для хранения данных с тестовыми характеристиками:

    * С именем `my-dataproc`.
    * С бакетом `dataproc-bucket`.
    * В зоне доступности `{{ zone-id }}`.
    * С сервисным аккаунтом `dataproc-sa`.
    * Образом версии `2.0`.
    * С компонентами `SPARK` и `YARN`.
    * С путем к публичной части SSH-ключа `/home/username/.ssh/id_rsa.pub`.
    * С подкластером с хостами-мастерами `master` и одним подкластером для обработки данных `compute`:

        * Класса `{{ host-class }}`.
        * С хранилищем на сетевых SSD-дисках (`{{ disk-type-example }}`) объемом 20 ГБ.
        * В подсети `{{ subnet-name }}`.
        * С публичным доступом.

    * В группе безопасности `{{ security-group }}`.
    * С защитой от случайного удаления кластера.

    Выполните следующую команду:

    ```bash
    {{ yc-dp }} cluster create my-dataproc \
       --bucket=dataproc-bucket \
       --zone={{ zone-id }} \
       --service-account-name=dataproc-sa \
       --version=2.0 \
       --services=SPARK,YARN \
       --ssh-public-keys-file=/home/username/.ssh/id_rsa.pub \
       --subcluster name="master",`
                   `role=masternode,`
                   `resource-preset={{ host-class }},`
                   `disk-type={{ disk-type-example }},`
                   `disk-size=20,`
                   `subnet-name={{ subnet-name }},`
                   `assign-public-ip=true \
       --subcluster name="compute",`
                   `role=computenode,`
                   `resource-preset={{ host-class }},`
                   `disk-type={{ disk-type-example }},`
                   `disk-size=20,`
                   `subnet-name={{ subnet-name }},`
                   `assign-public-ip=true \
       --security-group-ids={{ security-group }} \
       --deletion-protection=true
    ```

{% endlist %}
