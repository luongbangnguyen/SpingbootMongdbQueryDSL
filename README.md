# SpingbootMongdbQueryDSL
Project using spring boot and query dsl
Some time you want custom reposity mongodb of spring framework, queryDsl is a choice perfectly 
reference http://www.querydsl.com this project use gradle and springboot 

to custom repository you should extend class QueryDslPredicateExecutor<T>

interface CustomerRepository extends MongoRepository<Customer, String>,QueryDslPredicateExecutor<Customer>

to using QueryDsl example:
	BooleanBuilder builder = new BooleanBuilder();
	if (StringUtils.isNotBlank(fistname)) {
		builder.and(QCustomer.customer.firstName.eq(fistname));
	}
	
	if (StringUtils.isNoneBlank(lastname)) {
		builder.and(QCustomer.customer.lastName.eq(lastname));
	}
	return customerRepository.findAll(builder, pageable);
