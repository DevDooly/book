# consumer.poll() 과 session.timeout.ms 의 상관관계

## 개요

* consumer.poll()
* session.timeout.ms
* consumer.poll() 과 session.timeout.ms 의 상관관계
* heartbeat.interval.ms 와의 추가적인 관계

**참고자료**

 * https://docs.confluent.io/platform/current/installation/configuration/consumer-configs.html
 * https://codemia.io/knowledge-hub/path/kafka_is_giving_the_group_member_needs_to_have_a_valid_member_id_before_actually_entering_a_consumer_group 

## consumer.poll()

* kafka 의 consumer 는 poll() 메서드를 호출하여 브로커에서 메시지를 가져온다.
* poll() 을 일정 주기로 호출하지 않으면, 컨슈머는 자신이 아직 활성 상태(active) 임을 kafka 에게 알리지 못한다.
* poll() 호출하면, consumer 는 heartbeat 를 보내 자신의 상태를 유지한다.

## sesstion.timeout.ms

* 클라이언트 오류를 감지하는데 사용되는 기준 시간.
* 클라이언트가 브로커에 보내는 heartbeat 간격이 timeout 보다 길면 해당 클라이언트를 그룹에서 제거하고 재조정(rebalance) 을 시작한다.
* 기본값 : 45초(45000ms), 최소값 : 6초(6000ms)

## consumer.poll() 과 session.timeout.ms 의 상관관계

* consumer.poll() 은 session.timeout.ms 이하의 주기로 호출되어야 한다.
* 초과한다면 coordinator 은 컨슈머가 죽었다고 판단, 컨슈머의 파티션을 다른 컨슈머에게 재조정(rebalance) 한다.
* poll() 호출 간격이 너무 길어 session.timeout.ms 를 초과하면 consumer 는 강제 종료(kick-out) 될 수 있다.

## heartbeat.interval.ms 와의 추가적인 관계

* heartbeat.interval.ms 는 컨슈머가 정상 동작중임을 coordinator 에 알리는 주기이다.
* 보통 session.timeout.ms 의 1/3 이하로 설정해야 한다.
  (ex. sessiontimeout.ms = 30초 -> heartbeat.interval.ms = 10초 )
* 하지만 consumer.poll() 이 session.timeout.ms 를 넘어가면 heartbeat 도 정상적으로 보내지 않고, session 이 종료된다.

## 결론

* consumer.poll() 호출 주기는 반드시 session.timeout.ms 보다 짧아야 한다.
* 너무 긴 session.timeout.ms 는 장애 감지를 늦추고, 반대로 너무 짧으면 불필요한 rebalance 를 증가시킬 수 있다.
