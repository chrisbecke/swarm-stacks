STACK = $(notdir $(PWD))
DOMAIN ?= example.com

export DOMAIN

FILES = mitm-proxy-stack.yml

ps:
	docker stack ps $(STACK)

ls:
	docker stack services $(STACK)

deploy:
	docker stack deploy $(addprefix --compose-file ,$(FILES)) $(STACK)

clean:
	docker stack rm $(STACK)
