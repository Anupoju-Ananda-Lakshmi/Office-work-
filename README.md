package com.fincore.CommunicationService.cache;

import org.springframework.cache.annotation.Cacheable; 
import org.springframework.stereotype.Service;

import com.fincore.CommunicationService.model.FincoreSettings;
import com.fincore.CommunicationService.repository.FincoreSettingsRepository;

import lombok.RequiredArgsConstructor;

@Service
@RequiredArgsConstructor
public class ExternalApiConfigProvider {

	private final FincoreSettingsRepository repository;

	@Cacheable(value = "externalApiConfigs", key = "#functionality + '_' + #environment")
	public FincoreSettings getConfig(String functionality, String environment) {
		return repository.findByFunctionalityAndEnv(functionality, environment)
				.orElseThrow(() -> new RuntimeException("Configuration not found"));
	}
}
