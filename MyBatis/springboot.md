# My Batis Spring boot Starter
* Dependency 추가
    ```xml
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>2.2.2</version>
    </dependency>
    <!-- 버전은 알맞게 수정 -->
    ```

* @SpringBootApplication이 선언된 메인메서드가 존재하는 클래스 아래 sessionFactory 만들기
    ```java
    // mybatis sessionFactory
	@Bean
    SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
        SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();
        sessionFactory.setDataSource(dataSource);
        
        Resource[] res = new PathMatchingResourcePatternResolver().getResources("classpath:mapper/*.xml");
        sessionFactory.setMapperLocations(res);
        
        return sessionFactory.getObject();
    }
    ```

* mapper 만들기
    ```java
    package com.stim.model.mapper;

    import java.util.List;

    import org.apache.ibatis.annotations.Mapper;

    import com.stim.vo.GameVO;
    import com.stim.vo.UserVO;

    @Mapper
    public interface StimUserMapper {
        
        // 로그인
        UserVO getUserAccount(String user_id);

        // 회원가입
        void registerUser(UserVO uVo);
    }
    ```

* service 만들기
    ```java
    package com.stim.service.user;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.security.core.userdetails.UserDetailsService;
    import org.springframework.security.core.userdetails.UsernameNotFoundException;
    import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
    import org.springframework.stereotype.Service;
    import org.springframework.transaction.annotation.Transactional;

    import com.stim.model.mapper.StimUserMapper;
    import com.stim.vo.UserVO;

    import lombok.RequiredArgsConstructor;

    @Service
    @RequiredArgsConstructor	// 생성자 자동 생성 및 final 변수를 의존관계를 자동으로 설정해 준다.
    public class StimUserService implements UserDetailsService {
        
        private final StimUserMapper stimUserMapper;
        
        @Autowired
        private BCryptPasswordEncoder encoder;
        
        @Transactional	// 트랜잭션 보장이 된 메소드로 설정 해준다.
        public void registerUser(UserVO uVo){	// 회원가입
            uVo.setUser_password(encoder.encode(uVo.getPassword()));
            uVo.setUser_admin("N");
            stimUserMapper.registerUser(uVo);
        }
        
        @Override
        public UserVO loadUserByUsername(String user_id) throws UsernameNotFoundException {
            //여기서 받은 유저 패스워드와 비교하여 유저 정보 받기
            UserVO userVo = stimUserMapper.getUserAccount(user_id);
            
            if (userVo == null){
                throw new UsernameNotFoundException("User not authorized.");
            }
            
            return userVo;
        }

    }
    ```

* mapper.xml 만들기
    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <!-- mapper.java의 위치 잡아주기 -->
    <mapper namespace="com.stim.model.mapper.StimUserMapper">

        <!-- 로그인 -->
        <select id="getUserAccount" resultType="com.stim.vo.UserVO" parameterType="String">
            SELECT * FROM user_tbl
            WHERE user_id = #{user_id}
        </select>
        
        
        <!-- 회원가입 -->
        <insert id="registerUser" parameterType="com.stim.vo.UserVO">
            INSERT INTO user_tbl
            VALUES(user_code_seq.nextval, #{user_id}, #{user_password}, #{user_email}, #{user_phone}, 'noimage.jpg', 'N', #{user_nickname})
        </insert>
        
    </mapper>
    ```

* application.properties 설정
    ```
    # mybatis mapping
    # mapper.xml		.xml Mapping 시 resultType에 풀패키지명 대신 간략화 하는 설정
    mybatis.type-aliases-package:com.stim.model
    
    # mapper.xml location		mapper.xml 파일의 경로를 설정해주는 부분
    mybatis.mapper-locations: mapper/**/*.xml
    ```