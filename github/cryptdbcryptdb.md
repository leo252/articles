# CryptDB/cryptdb

[Original URL](https://github.com/CryptDB/cryptdb)

> CryptDB's website (including latest source code): <http://css.csail.mit.edu/cryptdb> Wiki: <https://github.com/burrows-labs/cryptdb-wiki/wiki/\_pages> Convention : When this document uses syntax like...

CryptDB's website (including latest source code): <http://css.csail.mit.edu/cryptdb>

Wiki: <https://github.com/burrows-labs/cryptdb-wiki/wiki/_pages>

Convention : When this document uses syntax like single-quote text single-quote + 'some-text-here' + 'a second example' it indicates that the reader is to use the value _without_ the single-quotes.

## [](https://github.com/CryptDB/cryptdb#-getting-started)##### Getting started

```
* Requirements:
 > Ubuntu 12.04, 13.04; Not tested on a different OS.
 > ruby
* Build CryptDB using the installation script.
 > note, this script will stop and start your mysql instance.
 > scripts/install.rb <path-to-cryptdb>
 + <path-to-cryptdb> should just be '.' if you are in the cryptdb
 directory
 + the script will likely require root privileges in order to
 install dependencies and UDFs.
* Username/Password
 By default cryptdb uses 'root' for the username and 'letmein'
 for the password. You can change this by modifying the source.
 + Tests: test/test_utils.hh
 + Shell: main/cdb_test.cc
 + Proxy: mysqlproxy/wrapper.lua
* Rebuildling CryptDB
 If you modify the source and want to rebuild, issue 'make' in the
 cryptdb directory. If you change the UDFs you will also need to do
 'make install' (which will likely require root privilege).
```

## [](https://github.com/CryptDB/cryptdb#-a-few-ways-to-run-cryptdb)# A few ways to run CryptDB

```
Set (or place in your .bashrc):
> export EDBDIR=/full/path/to/cryptdb/ 

I. Shell
 > To Start: obj/main/cdb_test ./shadow <some-database-name>
 > Type SQL queries that will be encrypted.

II. Proxy
 A) To Start: 
 > /path/to/cryptdb/bins/proxy-bin/bin/mysql-proxy \
 --plugins=proxy --event-threads=4 \
 --max-open-files=1024 \
 --proxy-lua-script=$EDBDIR/mysqlproxy/wrapper.lua \
 --proxy-address=127.0.0.1:3307 \
 --proxy-backend-addresses=localhost:3306

 B) Connect to CryptDB: (where root/letmein are username/password)
 mysql -u root -pletmein -h 127.0.0.1 -P 3307
 C) CREATE a database; USE it; Then type queries that will execute
 on encrypted data at the DB server!

III. Tests
 > To Start:
 obj/test/test queries plain proxy-single
```

## [](https://github.com/CryptDB/cryptdb#-failing-queries)###### Failing queries

```
Note that CryptDB is not a product, but just a more advanced research
prototype. It only has implemented a subset of SQL queries. For example,
it supports the regular MySQL client and a variety of queries you can
play with from this shell, Wordpress, and other apps. We did not test
it with other MySQL clients (e.g., PHP, Java) and these likely issue
some uncommon metadata query we did not implement. Feel free to
implement what's missing!


> Queries can fail for a few reasons.
 I) CryptDB can support the query, but we have not yet implemented it.
 This should throw an UNIMPLEMENTED exception.
 II) CryptDB can not support this type of query, likely for
 cryptographic reasons. The error should include a message telling
 you more about the issue.

 For example if you issue this query to the shell.
 SELECT 10_misconceptions_about_neural_networks.md aerofsgockerize.md artificial_neural_networks_for_beginners_»_loren_on_the_art_of_matlab.md automating_docker_logging_elasticsearch_logstash_kibana_and_logspout.md binaryjs.md cooking_with_docker_and_coreos_on_os_x.md create_a_rest_api_with_nodejs.md data data.json develop_command_line_application_using_nodejs.md even_easier_restful_api_with_nodejs_and_express_framework.md extending_layer_2_across_container_hosts.md file_input_to_gnuplot_through_python.md fix_time_machine_sparsebundle_nas_based_backup_errors.md flowchartjs.md get_coreos_logs_into_elk_in_5_minutes.md getting_started_·_bootstrap_table.md getting_started.md hacker_codex.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_create_a_fullscreen_video_opening_animation.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md html httpie_parse_url.sh httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md imjasonhghfs.md introduction.md jmschreipomegranate.md jquery_responsive_lightbox.md jquery_treetable.md jssequencediagrams.md just_a_theory.md let_your_applications_fly.md links_list.txt md micc83edittable.md null.md parse_urls_json.sh png reclaiming_a_timemachine_volume’s_disk_space.md setting_up_nginx_with_docker.md silentbicycleguff.md sqljs.md the_incomplete_collection_of_nodejs_performance_tips_—_node_and_beyond.md the_power_of_pcap__retrospection_in_practice.md the_what_how_and_why_of_single_var_pattern_in_javascript.md tidy time_machine_for_every_unix_out_there.md timothycrosleyhug.md top_15_facebook_open_source_projects_you_must_know_and_use_for_your_maker’s_projects.md url_to_filename.csv using_feathersjs_as_an_open_source_alternative_to_firebase_—_all_about_feathersjs.md using_the_office_ui_fabric_with_angular_js_to_create_an_office_addin.md write_vanilla_comments_that_automagically_convert_into_full_test_spec_files.md zenit.md FROM t WHERE x + 10 < 50

 You should get feedback. You will see that the feedback
 tells you the desired security level for the operation at
 each onion, and the current security level of each argument
 for each onion.

 In the case of this query, you can see that it would like
 to compare order with onion 1 (OPE), 4 (PLAIN) or 6 (WAIT).

 But if you look at it's first child "additive", you can see
 that it would like to use the HOM onion. Because the HOM
 onion is not supported by the order operation, the query
 fails and gives you this error message.

 Look to the CryptDB paper for more information regarding
 how MySQL operations correspond to the cryptographic onions.
 III) State corruption (ie a bug in our code), this will likely lead
 to a crash.
 IV) Invalid query; problem with the query syntax.
```

## [](https://github.com/CryptDB/cryptdb#-misc)########## Misc

```
> The benchmarks in the CryptDB paper are based on an older version of
 CryptDB. We have added new features and functionality to the current
 version which we have not yet optimized (but it is part of our
 roadmap).
> For example queries, take a look at our tests test/TestQueries.cc
> Consider connecting directly to MySQL to see what is in the raw
 database.
> Resetting state (e.g., after running tests)
 > Take a look at scripts/refresh.sh ; you probably want that.
 > If you create a database in cryptdb and then reset
 the state without first dropping the database from _within_
 cryptdb; you will need to drop it from the regular mysql client.
> Modifying the UDFs.
 + If you make changes to edb/udf.cc, you must reinstall the UDFs
 before your changes will take affect.
 > sudo service mysql stop
 > sudo make install
 > sudo service mysql start
> This new version of CryptDB is only single principal. We are in the
 process of developling a new platform that more affectively addresses
 multiple principals.
```
