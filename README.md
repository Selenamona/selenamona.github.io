# [Selenamona](https://selenamona.github.io/)


1：查看当前镜像库：

gem sources -l

2： 修改gem的库地址：

gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/

brew info ruby


#########################################

gem install jekyll

<!-- sudo gem install --user-install bundler jekyll 

export PATH=${PATH}:/Users/tinytongtong/.gem/ruby/2.6.0/bin  -->

gem install bundler

bundle install

jekyll build

jekyll serve


# Gemfile文件

Gemfile是一个用于描述gem之间依赖的文件。gem是一堆Ruby代码的集合，它能够为我们提供调用。

Gemfile是可通过Bundler创建。

## pagination

Gemfile 文件中添加：`gem "jekyll-paginate"`，运行 bundle install 

运行 jekyll build/serve 时，出现该问题：

```
Deprecation: You appear to have pagination turned on, but you haven’t included the jekyll-paginate gem. Ensure you have gems: [jekyll-paginate] in your configuration file.
```

解决：配置文件_config.yml使用了 paginate 配置项，所以需要添加一行：

```
gems: [jekyll-paginate]
```