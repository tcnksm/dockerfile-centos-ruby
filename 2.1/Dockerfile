FROM tcnksm/centos-buildpack-deps:6

# To Build ruby 2.1, Autoconf version 2.67 or higher is required
RUN mkdir -p /usr/src/autoconf \
    && curl -SL "http://ftp.gnu.org/gnu/autoconf/autoconf-2.69.tar.gz " \
    | tar -xzC /usr/src/autoconf \
    && cd /usr/src/autoconf/autoconf-2.69 \
    && ./configure \
    && make \
    && make install \
    && rm -r /usr/src/autoconf    

ENV RUBY_MAJOR 2.1
ENV RUBY_VERSION 2.1.4

# some of ruby's build scripts are written in ruby
# we purge this later to make sure our final image uses what we just built
RUN yum -y update && yum -y install ruby && yum clean all \
	&& mkdir -p /usr/src/ruby \
	&& curl -SL "http://cache.ruby-lang.org/pub/ruby/$RUBY_MAJOR/ruby-$RUBY_VERSION.tar.bz2" \
		| tar -xjC /usr/src/ruby --strip-components=1 \
	&& cd /usr/src/ruby \
	&& autoconf \
	&& ./configure --disable-install-doc \
	&& make -j"$(nproc)" \
	&& yum remove -y ruby \
	&& make install \
	&& rm -r /usr/src/ruby

# skip installing gem documentation
RUN echo 'gem: --no-rdoc --no-ri' >> "$HOME/.gemrc"

# install things globally, for great justice
ENV GEM_HOME /usr/local/bundle
ENV PATH $GEM_HOME/bin:$PATH
RUN gem install bundler \
	&& bundle config --global path "$GEM_HOME" \
	&& bundle config --global bin "$GEM_HOME/bin"

# don't create ".bundle" in all our apps
ENV BUNDLE_APP_CONFIG $GEM_HOME

CMD [ "irb" ]
