log4j_detector_on_java
=========

Используется сканер проекта:
https://github.com/mergebase/log4j-detector

Используется автономная сборка java openlogic-openjdk-jre
https://www.openlogic.com/

[Readme-ENG](./README.md)

Особенности роли
--------------
Роль содержит в files бинарные файлы:
openlogic-openjdk-jre - автономная java
log4j-detector - сам сканер уязвимости

Роль написанно таким образом, что может быть запущена с автономной версии java или
использовать уже установленную java в системе.
Примеры запуска будут ниже.

Роль умеет собирать отчеты сканирования со всех машин, на ноду управления.
Роль имеет возможность удаление всех установленных файл после сканирования или
когда это будет необходимо.

Переменные используемые в роли
--------------

#### log4j_detector_on_java_openjdk

    log4j_detector_on_java_openjdk:
      version: '8u292-b10'
      checksum: 'b51ef522d35b23ee2469bb2324b365ffdd9773420329ece675bdfcf1b9365ae4'
      install_from_files_directory: yes
      remove: no

version - версия openlogic-openjdk-jre архива.
checksum - хэш sha256 архива openlogic-openjdk-jre.
install_from_files_directory - Производить установку openlogic-openjdk-jre на машину или нет.
remove - Выполнять удаление всей дериктории, куда устанавливается log4j-detector.

#### log4j_detector_on_java_log4

    log4j_detector_on_java_log4j:
      version: '2021.12.22'
      checksum: 'f19677f80589616a6c96f20c7d322ac0294950f06cfc1c880b77c34887aa0275'
      nice: 0
      opts: '-Xmx1024m'
      install_from_files_directory: yes
      path_search: '/'
      argument: --exclude='["/home"]'
      remove: no

version - версия log4j-detector 
checksum - хэш 256 файла jar log4j-detector .
nice - Задать приоритет запущенному процессу сканирование. По умолчанию 0 .
opts - Задать доплнительные параметры для java. По умолчанию ограничение памяти -Xmx512m .
install_from_files_directory - Производить установку log4j-detector на машину или нет.
path_search - Директория сканирование для log4j. По умолчанию / .
argument - Дополнительный аргумент запуска log4j.
remove - Выполнять удаление log4j-detector.

#### log4j_detector_on_java_file_result

    log4j_detector_on_java_file_result:
      path_report_on_controlled_node:
      create: yes
      filename: log4j_result
      csv: yes

path_report_on_controlled_node - Директория ну уарпвляющей ноде ansible, куда будут собираться все отчеты.
create - Создавать отчет или нет.
filename - С каким именем, будет формироваться файл отчета. По умолчанию log4j_result
csv - Формировать отчет в csv. ( сейчас только CSV, планируется сделать выбор json )

- name: Fetch log4j CSV reports from hosts
  fetch:
    src: "{{ log4j_detector_on_java_dir_work }}/{{ log4j_detector_on_java_result.filename | d('log4j_result') }}.csv"
    dest: "{{ log4j_detector_on_java_result.path_report_on_controlled_node | d('/tmp/log4j_detector_report/')}}/{{ inventory_hostname }}_{{ ansible_default_ipv4.address }}_{{ log4j_detector_on_java_result.filename | d('log4j_result') }}.csv"
    flat: yes
  when:
    - log4j_detector_on_java_file_result.create
    - log4j_detector_on_java_file_result.csv

#### log4j_detector_on_java_log4

    log4j_detector_on_java_dir_work: /opt/log4j_detector_on_java
    log4j_detector_on_java_dir_openjdk: "{{ log4j_detector_on_java_dir_work }}/openlogic-openjdk-jre-{{ log4j_detector_on_java_openjdk.version }}-linux"
    log4j_detector_on_java_path_java: "{{ log4j_detector_on_java_dir_openjdk }}/bin/java"

    log4j_detector_on_java_dir_create:
      - "{{ log4j_detector_on_java_dir_work }}"
      - "{{ log4j_detector_on_java_dir_openjdk }}"

Пример плейбука для запуска роли
----------------

    - hosts: all
      roles:
         - { role: log4j_detector_on_java }

License
-------

Author Information
------------------

Vladimir Zhurkin
