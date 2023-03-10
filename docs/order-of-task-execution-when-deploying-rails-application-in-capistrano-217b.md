# 在 capistrano 中部署 Rails 应用程序时的任务执行顺序

> 原文：<https://dev.to/nasum/order-of-task-execution-when-deploying-rails-application-in-capistrano-217b>

检查在 capistrano 中使用 Rails 插件和不使用时的区别。

与卡皮斯特拉诺有关的宝石如下。

*   卡皮斯特拉诺铁路公司
*   卡皮斯特拉诺-rbenv
*   卡皮斯特拉诺-邦德勒

我觉得那些 gem 用 Rails 的时候一般。

任务执行顺序的区别如下。

```
** Invoke production (first_time)                               ** Invoke production (first_time)
** Execute production                                           ** Execute production
** Invoke load:defaults (first_time)                            ** Invoke load:defaults (first_time)
** Execute load:defaults                                        ** Execute load:defaults
                                                              > ** Invoke rbenv:validate (first_time)
                                                              > ** Execute rbenv:validate
                                                              > ** Invoke rbenv:map_bins (first_time)
                                                              > ** Execute rbenv:map_bins
                                                              > ** Invoke bundler:map_bins (first_time)
                                                              > ** Execute bundler:map_bins
                                                              > ** Invoke deploy:set_rails_env (first_time)
                                                              > ** Execute deploy:set_rails_env
                                                              > ** Invoke deploy:set_linked_dirs (first_time)
                                                              > ** Execute deploy:set_linked_dirs
                                                              > ** Invoke deploy:set_rails_env
** Invoke deploy (first_time)                                   ** Invoke deploy (first_time)
** Execute deploy                                               ** Execute deploy
** Invoke deploy:starting (first_time)                          ** Invoke deploy:starting (first_time)
** Execute deploy:starting                                      ** Execute deploy:starting
** Invoke deploy:check (first_time)                             ** Invoke deploy:check (first_time)
** Invoke git:check (first_time)                                ** Invoke git:check (first_time)
** Invoke git:wrapper (first_time)                              ** Invoke git:wrapper (first_time)
** Execute git:wrapper                                          ** Execute git:wrapper
** Execute git:check                                            ** Execute git:check
** Execute deploy:check                                         ** Execute deploy:check
** Invoke deploy:check:directories (first_time)                 ** Invoke deploy:check:directories (first_time)
** Execute deploy:check:directories                             ** Execute deploy:check:directories
** Invoke deploy:check:linked_dirs (first_time)                 ** Invoke deploy:check:linked_dirs (first_time)
** Execute deploy:check:linked_dirs                             ** Execute deploy:check:linked_dirs
** Invoke deploy:check:make_linked_dirs (first_time)            ** Invoke deploy:check:make_linked_dirs (first_time)
** Execute deploy:check:make_linked_dirs                        ** Execute deploy:check:make_linked_dirs
** Invoke deploy:check:linked_files (first_time)                ** Invoke deploy:check:linked_files (first_time)
** Execute deploy:check:linked_files                            ** Execute deploy:check:linked_files
** Invoke deploy:set_previous_revision (first_time)             ** Invoke deploy:set_previous_revision (first_time)
** Execute deploy:set_previous_revision                         ** Execute deploy:set_previous_revision
** Invoke deploy:started (first_time)                           ** Invoke deploy:started (first_time)
** Execute deploy:started                                       ** Execute deploy:started
** Invoke deploy:updating (first_time)                          ** Invoke deploy:updating (first_time)
** Invoke deploy:new_release_path (first_time)                  ** Invoke deploy:new_release_path (first_time)
** Execute deploy:new_release_path                              ** Execute deploy:new_release_path
** Invoke git:create_release (first_time)                       ** Invoke git:create_release (first_time)
** Invoke git:update (first_time)                               ** Invoke git:update (first_time)
** Invoke git:clone (first_time)                                ** Invoke git:clone (first_time)
** Invoke git:wrapper                                           ** Invoke git:wrapper
** Execute git:clone                                            ** Execute git:clone
** Execute git:update                                           ** Execute git:update
** Execute git:create_release                                   ** Execute git:create_release
** Execute deploy:updating                                      ** Execute deploy:updating
** Invoke deploy:set_current_revision (first_time)              ** Invoke deploy:set_current_revision (first_time)
** Invoke git:set_current_revision (first_time)                 ** Invoke git:set_current_revision (first_time)
** Execute git:set_current_revision                             ** Execute git:set_current_revision
** Execute deploy:set_current_revision                          ** Execute deploy:set_current_revision
** Invoke deploy:symlink:shared (first_time)                    ** Invoke deploy:symlink:shared (first_time)
** Execute deploy:symlink:shared                                ** Execute deploy:symlink:shared
** Invoke deploy:symlink:linked_files (first_time)              ** Invoke deploy:symlink:linked_files (first_time)
** Execute deploy:symlink:linked_files                          ** Execute deploy:symlink:linked_files
** Invoke deploy:symlink:linked_dirs (first_time)               ** Invoke deploy:symlink:linked_dirs (first_time)
** Execute deploy:symlink:linked_dirs                           ** Execute deploy:symlink:linked_dirs
** Invoke deploy:updated (first_time)                           ** Invoke deploy:updated (first_time)
                                                              > ** Invoke bundler:install (first_time)
                                                              > ** Execute bundler:install
** Execute deploy:updated                                       ** Execute deploy:updated
                                                              > ** Invoke deploy:compile_assets (first_time)
                                                              > ** Invoke deploy:set_rails_env
                                                              > ** Execute deploy:compile_assets
                                                              > ** Invoke deploy:assets:precompile (first_time)
                                                              > ** Execute deploy:assets:precompile
                                                              > ** Invoke deploy:assets:backup_manifest (first_time)
                                                              > ** Execute deploy:assets:backup_manifest
                                                              > ** Invoke deploy:cleanup_assets (first_time)
                                                              > ** Invoke deploy:set_rails_env
                                                              > ** Execute deploy:cleanup_assets
                                                              > ** Invoke deploy:normalize_assets (first_time)
                                                              > ** Invoke deploy:set_rails_env
                                                              > ** Execute deploy:normalize_assets
                                                              > ** Invoke deploy:migrate (first_time)
                                                              > ** Invoke deploy:set_rails_env
                                                              > ** Execute deploy:migrate
** Invoke deploy:publishing (first_time)                        ** Invoke deploy:publishing (first_time)
** Execute deploy:publishing                                    ** Execute deploy:publishing
** Invoke deploy:symlink:release (first_time)                   ** Invoke deploy:symlink:release (first_time)
** Execute deploy:symlink:release                               ** Execute deploy:symlink:release
** Invoke deploy:published (first_time)                         ** Invoke deploy:published (first_time)
** Execute deploy:published                                     ** Execute deploy:published
** Invoke deploy:finishing (first_time)                         ** Invoke deploy:finishing (first_time)
** Execute deploy:finishing                                     ** Execute deploy:finishing
** Invoke deploy:cleanup (first_time)                           ** Invoke deploy:cleanup (first_time)
** Execute deploy:cleanup                                       ** Execute deploy:cleanup
** Invoke deploy:finished (first_time)                          ** Invoke deploy:finished (first_time)
** Execute deploy:finished                                      ** Execute deploy:finished
** Invoke deploy:log_revision (first_time)                      ** Invoke deploy:log_revision (first_time)
** Execute deploy:log_revision                                  ** Execute deploy:log_revision 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到添加了预编译和迁移。