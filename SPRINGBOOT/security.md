# Config 파일 예시
    ```java
    package com.stim.config;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
    import org.springframework.security.web.SecurityFilterChain;
    import org.springframework.web.cors.CorsConfiguration;
    import org.springframework.web.cors.CorsConfigurationSource;
    import org.springframework.web.cors.UrlBasedCorsConfigurationSource;

    @Configuration
    @EnableWebSecurity
    public class SecurityConfig{
        
        // 비밀번호 암호화를 위한 메서드
        @Bean	// 스프링 부트 버전업에 따라 public 생략이 가능한듯 하다.
        BCryptPasswordEncoder encoder() {
            return new BCryptPasswordEncoder();
        }
        
        @Bean
        public
        SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
            http.authorizeRequests()
    //				.antMatchers("/user/**").authenticated()     
    //						// user주소에 대해서 인증을 요구한다
    //				.antMatchers("/manager/**").access("hasRole('ROLE_MANAGER') or hasRole('ROLE_ADMIN')")	
    //						// manager주소는 ROLE_MANAGER권한이나 ROLE_ADMIN권한이 있어야 접근할 수 있다.
    //				.antMatchers("/admin/**").hasRole("ROLE_ADMIN")	
    //						// admin주소는 ROLE_ADMIN권한이 있어야 접근할 수 있다.
                    .anyRequest().permitAll()	// 나머지주소는 인증없이 접근 가능하다
            .and()
                .formLogin()					// form기반의 로그인인 경우
                    .loginPage("/loginForm")		// 인증이 필요한 URL에 접근하면 /loginForm으로 이동
                    .usernameParameter("user_id")		// 로그인 시 form에서 가져올 값(id, email 등이 해당)
                    .passwordParameter("user_password")	// 로그인 시 form에서 가져올 값
                    .loginProcessingUrl("/login")	// 로그인을 처리할 맵핑 입력
                    .defaultSuccessUrl("/")			// 로그인 성공하면 해당 맵핑으로 이동
                    .failureUrl("/loginForm")		//로그인 실패 시 /loginForm으로 이동
            .and()
                .logout()						// logout할 경우
                    .logoutUrl("/logout")			// 로그아웃을 처리할 URL 입력
                    .logoutSuccessUrl("/")	// 로그아웃 성공 시 이동할 맵핑으로 이동
                    .invalidateHttpSession(true)	// 세션 초기화
            .and()
                .cors().configurationSource(corsConfigurationSource())
            .and()
                .csrf().disable();
            // Cross site Request forgery로 사이즈간 위조 요청인데, 즉 정상적인 사용자가 의도치 않은 위조요청을 보내는 것을 의미
            // GET요청을 제외한 상태를 변화시킬 수 있는 POST, PUT, DELETE 요청으로부터 보호하는 csrf를 disable해서 post형식으로 회원가입이 가능하게 한다.

            return http.build();
        }
        
        
        // CORS 허용 적용
        @Bean
        CorsConfigurationSource corsConfigurationSource() {
            CorsConfiguration configuration = new CorsConfiguration();

            configuration.addAllowedOrigin("*");	// 허용할 URL
            configuration.addAllowedHeader("*");	// 허용할 Header
            configuration.addAllowedMethod("*");	// 허용할 Http Method
            configuration.setAllowCredentials(true);

            UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
            source.registerCorsConfiguration("/**", configuration);
            return source;
        }
        
    }
    ```